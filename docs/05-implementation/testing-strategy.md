# Testing Strategy – Chiến lược Kiểm thử

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  

---

## 1. Testing Philosophy

### 1.1 Test Pyramid

```
         /\
        /  \
       / E2E \       ← Ít nhất, chậm nhất, đắt nhất
      /--------\     
     /Integration\   ← Vừa phải
    /--------------\
   /   Unit Tests   \ ← Nhiều nhất, nhanh nhất, rẻ nhất
  /------------------\
```

| Layer | Tỷ lệ | Tool | Môi trường |
|---|---|---|---|
| Unit Tests | 60% | Jest | Local + CI |
| Integration Tests | 30% | Jest + Testcontainers | CI |
| E2E Tests | 10% | Playwright | Staging |

### 1.2 Coverage Targets

| Loại | Target | Minimum |
|---|---|---|
| Business logic (services) | 80% | 70% |
| Controllers | 70% | 60% |
| Utilities | 90% | 80% |
| Overall | 75% | 65% |

---

## 2. Unit Testing

### 2.1 Scope
- Service layer business logic
- Utility functions
- Data transformations
- Validation rules
- State machine transitions (order status)

### 2.2 Test Patterns

```typescript
// order.service.spec.ts
describe('OrderService', () => {
  let service: OrderService;
  let mockOrderRepo: jest.Mocked<OrderRepository>;
  let mockInventoryService: jest.Mocked<InventoryService>;

  beforeEach(() => {
    mockOrderRepo = createMock<OrderRepository>();
    mockInventoryService = createMock<InventoryService>();
    service = new OrderService(mockOrderRepo, mockInventoryService);
  });

  describe('createOrder', () => {
    it('should reserve inventory when order is created', async () => {
      // Arrange
      const dto: CreateOrderDto = {
        addressId: 'addr-uuid',
        paymentMethod: 'vnpay',
        items: [{ variantId: 'var-uuid', qty: 1 }]
      };
      mockInventoryService.checkAvailability.mockResolvedValue(true);
      mockOrderRepo.create.mockResolvedValue(mockOrder);

      // Act
      const result = await service.createOrder('customer-uuid', dto);

      // Assert
      expect(mockInventoryService.reserveStock).toHaveBeenCalledWith('var-uuid', 1);
      expect(result.status).toBe('new');
    });

    it('should throw InsufficientStockError when stock is 0', async () => {
      mockInventoryService.checkAvailability.mockResolvedValue(false);
      
      await expect(
        service.createOrder('customer-uuid', mockCreateOrderDto)
      ).rejects.toThrow(InsufficientStockError);
    });
  });

  describe('cancelOrder', () => {
    it('should release inventory reservation when order is cancelled', async () => {
      const order = { ...mockOrder, status: 'confirmed' };
      mockOrderRepo.findById.mockResolvedValue(order);

      await service.cancelOrder('order-uuid', { reason: 'Customer request' });

      expect(mockInventoryService.releaseReservation).toHaveBeenCalled();
    });

    it('should not allow cancellation after shipping', async () => {
      const order = { ...mockOrder, status: 'shipping' };
      mockOrderRepo.findById.mockResolvedValue(order);

      await expect(
        service.cancelOrder('order-uuid', { reason: 'Too late' })
      ).rejects.toThrow(InvalidStatusTransitionError);
    });
  });
});
```

---

## 3. Integration Testing

### 3.1 Scope
- API endpoints (controller → service → database)
- Database queries và transactions
- Webhook handlers
- External service mocks

### 3.2 Setup với Testcontainers

```typescript
// test/setup.ts
import { PostgreSqlContainer } from '@testcontainers/postgresql';
import { RedisContainer } from '@testcontainers/redis';

let pgContainer: StartedPostgreSqlContainer;
let redisContainer: StartedRedisContainer;

beforeAll(async () => {
  pgContainer = await new PostgreSqlContainer('postgres:15').start();
  redisContainer = await new RedisContainer().start();
  
  process.env.DATABASE_URL = pgContainer.getConnectionUri();
  process.env.REDIS_URL = `redis://${redisContainer.getHost()}:${redisContainer.getMappedPort(6379)}`;
  
  // Run migrations
  await runMigrations();
});

afterAll(async () => {
  await pgContainer.stop();
  await redisContainer.stop();
});
```

### 3.3 API Integration Test

```typescript
// order.e2e-spec.ts
describe('Order API', () => {
  let app: INestApplication;
  let authToken: string;

  beforeAll(async () => {
    app = await createTestApp();
    authToken = await loginAndGetToken(app);
  });

  describe('POST /api/v1/orders', () => {
    it('should create order and reserve inventory', async () => {
      // Setup: product với tồn kho
      await createProductWithInventory({ available: 5 });
      
      const response = await request(app.getHttpServer())
        .post('/api/v1/orders')
        .set('Authorization', `Bearer ${authToken}`)
        .send({
          addressId: testAddressId,
          paymentMethod: 'vnpay',
          items: [{ variantId: testVariantId, qty: 1 }]
        });

      expect(response.status).toBe(201);
      expect(response.body.data.status).toBe('new');
      
      // Verify inventory was reserved
      const inventory = await getInventory(testVariantId);
      expect(inventory.reserved).toBe(1);
    });
  });

  describe('Webhook: POST /webhooks/payment/vnpay', () => {
    it('should update order status on successful payment', async () => {
      const order = await createPendingOrder();
      const payload = buildVNPayWebhookPayload({
        orderId: order.orderNumber,
        status: 'success'
      });
      const signature = signVNPayPayload(payload, process.env.VNPAY_HASH_SECRET);

      const response = await request(app.getHttpServer())
        .post('/webhooks/payment/vnpay')
        .set('vnp-SecureHash', signature)
        .send(payload);

      expect(response.status).toBe(200);
      
      const updatedOrder = await getOrder(order.id);
      expect(updatedOrder.status).toBe('confirmed');
    });

    it('should reject webhook with invalid signature', async () => {
      const response = await request(app.getHttpServer())
        .post('/webhooks/payment/vnpay')
        .set('vnp-SecureHash', 'invalid-signature')
        .send({ vnp_TxnRef: 'some-ref' });

      expect(response.status).toBe(400);
    });
  });
});
```

---

## 4. E2E Testing (Playwright)

### 4.1 Critical User Journeys

```typescript
// tests/e2e/checkout.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Checkout Flow', () => {
  test('Customer can complete purchase with VNPAY', async ({ page }) => {
    // 1. Browse products
    await page.goto('/');
    await page.fill('[data-testid="search-input"]', 'iPhone 15');
    await page.click('[data-testid="search-button"]');
    await expect(page.locator('[data-testid="product-card"]').first()).toBeVisible();

    // 2. View product detail
    await page.click('[data-testid="product-card"]');
    await expect(page).toHaveURL(/\/products\//);

    // 3. Select variant and add to cart
    await page.click('[data-testid="variant-256gb"]');
    await page.click('[data-testid="add-to-cart"]');
    await expect(page.locator('[data-testid="cart-count"]')).toHaveText('1');

    // 4. Proceed to checkout
    await page.click('[data-testid="go-to-checkout"]');
    await page.fill('[data-testid="phone-input"]', '0901234567');
    await page.fill('[data-testid="name-input"]', 'Test Customer');
    // ... fill address

    // 5. Apply voucher
    await page.fill('[data-testid="voucher-input"]', 'TEST10');
    await page.click('[data-testid="apply-voucher"]');
    await expect(page.locator('[data-testid="discount-amount"]')).toBeVisible();

    // 6. Select payment method
    await page.click('[data-testid="payment-vnpay"]');

    // 7. Confirm order
    await page.click('[data-testid="confirm-order"]');
    await expect(page).toHaveURL(/\/vnpay/);
  });
});
```

### 4.2 E2E Test Coverage

| User Journey | Priority | Status |
|---|---|---|
| Đăng ký tài khoản | P0 | Phải có trước Go-live |
| Tìm kiếm & lọc sản phẩm | P0 | Phải có trước Go-live |
| Thêm vào giỏ, checkout COD | P0 | Phải có trước Go-live |
| Checkout với VNPAY | P0 | Phải có trước Go-live |
| Checkout với MoMo | P0 | Phải có trước Go-live |
| Theo dõi đơn hàng | P1 | Phải có trước Go-live |
| Yêu cầu đổi trả | P1 | Phải có trước Go-live |
| Admin: Tạo sản phẩm | P1 | Phải có trước Go-live |
| Admin: Xử lý đơn hàng | P1 | Phải có trước Go-live |
| Áp dụng voucher | P2 | Trong 2 tuần sau Go-live |
| Trade-in | P3 | Giai đoạn 2 |

---

## 5. Performance Testing

### 5.1 Load Testing với k6

```javascript
// load-tests/checkout.js
import http from 'k6/http';
import { check, sleep } from 'k6';

export const options = {
  scenarios: {
    normal_load: {
      executor: 'constant-vus',
      vus: 100,
      duration: '5m',
    },
    peak_load: {
      executor: 'ramping-vus',
      startVUs: 0,
      stages: [
        { duration: '2m', target: 500 },
        { duration: '5m', target: 500 },
        { duration: '2m', target: 0 },
      ],
    },
  },
  thresholds: {
    http_req_duration: ['p(95)<200', 'p(99)<500'],
    http_req_failed: ['rate<0.01'],
  },
};

export default function () {
  // Product listing
  const products = http.get(`${BASE_URL}/api/v1/products`);
  check(products, {
    'products status 200': (r) => r.status === 200,
    'products response time < 200ms': (r) => r.timings.duration < 200,
  });

  sleep(1);

  // Product detail
  const detail = http.get(`${BASE_URL}/api/v1/products/apple-iphone-15-pro`);
  check(detail, {
    'detail status 200': (r) => r.status === 200,
    'detail response time < 150ms': (r) => r.timings.duration < 150,
  });
}
```

### 5.2 Performance Benchmarks

| Endpoint | P95 Target | P99 Target |
|---|---|---|
| GET /products | 200ms | 500ms |
| GET /products/:slug | 150ms | 300ms |
| POST /cart/items | 200ms | 400ms |
| POST /orders | 500ms | 1000ms |
| GET /admin/orders | 800ms | 2000ms |

---

## 6. Security Testing

### 6.1 OWASP ZAP Automated Scan

```bash
# Chạy OWASP ZAP baseline scan
docker run -t owasp/zap2docker-stable zap-baseline.py \
  -t https://staging.bandienthoai.vn \
  -r zap-report.html \
  -I  # Ignore warnings (only fail on high)
```

### 6.2 Manual Security Testing Checklist

Trước Go-live, phải kiểm tra thủ công:

**Authentication & Authorization:**
- [ ] Không thể xem đơn hàng của người khác (IDOR test)
- [ ] Không thể truy cập admin endpoints khi là customer
- [ ] JWT token hết hạn → redirect đăng nhập
- [ ] Brute force lock sau 5 lần sai

**Payment Security:**
- [ ] Webhook với signature sai → 400 rejected
- [ ] Không thể thay đổi amount khi đã tạo payment link
- [ ] Double payment prevention (idempotency)

**Input Validation:**
- [ ] SQL injection trong search query
- [ ] XSS trong product review / order note
- [ ] Path traversal trong file upload

---

## 7. UAT (User Acceptance Testing)

### 7.1 UAT Plan

| Sprint | UAT Scope | Participants | Duration |
|---|---|---|---|
| Sprint 3 (alpha) | Catalog, cart, order creation | Tech Lead, PM | 2 ngày |
| Sprint 5 (beta) | Full flow: checkout, payment, shipping | PM, Business Owner, Key Staff | 3 ngày |
| Sprint 7 (RC) | Regression + full flow | All stakeholders | 5 ngày |

### 7.2 UAT Scenarios (Critical)

| Scenario | Steps | Expected Result |
|---|---|---|
| **Mua hàng COD** | Chọn SP → Thêm giỏ → Checkout → COD → Confirm | Đơn tạo thành công, email gửi đi |
| **Mua hàng VNPAY** | Checkout → VNPAY payment → Callback | Đơn chuyển trạng thái confirmed |
| **Hủy đơn** | Tạo đơn → Hủy → Kiểm tra tồn kho | Tồn kho được hoàn lại |
| **Tracking giao hàng** | Tạo vận đơn → Xem tracking → Webhook update | Trạng thái cập nhật chính xác |
| **Yêu cầu đổi trả** | Mở ticket đổi trả → Upload ảnh → Submit | Ticket được tạo, email xác nhận |
| **Quản lý tồn kho** | Nhập hàng PO → Verify tồn thay đổi | On_hand tăng chính xác |

### 7.3 Bug Severity Classification

| Severity | Mô tả | SLA giải quyết |
|---|---|---|
| **Critical** | System down, data loss, payment failure | Trong 4 giờ |
| **High** | Core feature không hoạt động | Trong 1 ngày |
| **Medium** | Feature không hoạt động đúng, có workaround | Trong 3 ngày |
| **Low** | UI issue, minor UX | Trước Go-live nếu có thể |

### 7.4 Go/No-Go Criteria

| Loại | Điều kiện Go-live |
|---|---|
| Critical bugs | 0 critical bugs open |
| High bugs | ≤ 2 high bugs với workaround được biết |
| Test coverage | ≥ 70% overall coverage |
| Performance | P95 của all P0 endpoints < target |
| Security | 0 critical/high OWASP findings |
| UAT sign-off | Business owner phê duyệt bằng văn bản |
