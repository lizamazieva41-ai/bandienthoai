# Development Guidelines – Quy trình Phát triển

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  

---

## 1. Coding Standards

### 1.1 Ngôn ngữ & Framework

| Layer | Technology | Version |
|---|---|---|
| Backend | Node.js + TypeScript | Node 20 LTS, TS 5.x |
| Frontend | Next.js + TypeScript | Next.js 14+ |
| Database ORM | Prisma | 5.x |
| Styling | Tailwind CSS | 3.x |

### 1.2 TypeScript Standards

```typescript
// ✅ GOOD: Explicit types, no any
interface CreateOrderDto {
  addressId: string;
  paymentMethod: PaymentMethod;
  items: OrderItemDto[];
}

// ❌ BAD: any type
function processOrder(order: any) { ... }

// ✅ GOOD: Readonly untuk immutable data
type OrderStatus = 'new' | 'confirmed' | 'packing' | 'shipping' | 'delivered' | 'completed' | 'cancelled';

// ✅ GOOD: Proper error handling
class OrderNotFoundError extends Error {
  constructor(orderId: string) {
    super(`Order ${orderId} not found`);
    this.name = 'OrderNotFoundError';
  }
}
```

### 1.3 Naming Conventions

| Element | Convention | Ví dụ |
|---|---|---|
| Variables/Functions | camelCase | `getUserById`, `orderTotal` |
| Classes/Interfaces | PascalCase | `OrderService`, `CreateOrderDto` |
| Constants | UPPER_SNAKE_CASE | `MAX_RETRY_ATTEMPTS` |
| Database tables | snake_case | `order_item`, `product_variant` |
| API endpoints | kebab-case | `/api/v1/order-items` |
| File names | kebab-case | `order.service.ts` |
| Environment variables | UPPER_SNAKE_CASE | `DATABASE_URL` |

### 1.4 File Structure

```
src/
├── modules/
│   ├── order/
│   │   ├── dto/
│   │   │   ├── create-order.dto.ts
│   │   │   └── update-order.dto.ts
│   │   ├── order.controller.ts
│   │   ├── order.service.ts
│   │   ├── order.repository.ts
│   │   ├── order.module.ts
│   │   └── order.service.spec.ts
│   └── product/
│       └── ...
├── common/
│   ├── decorators/
│   ├── guards/
│   ├── interceptors/
│   ├── pipes/
│   └── filters/
├── config/
├── database/
│   ├── migrations/
│   └── seeds/
└── main.ts
```

### 1.5 Clean Code Principles

- **Single Responsibility:** Mỗi function làm 1 việc
- **Function length:** Tối đa 30–40 dòng
- **Cyclomatic complexity:** Tối đa 10 per function
- **DRY:** Không duplicate logic, tạo shared utilities
- **Comments:** Code phải tự mô tả; comment khi có logic phức tạp, không comment hiển nhiên

```typescript
// ❌ BAD comment
// Get user by id
const user = await getUserById(id);

// ✅ GOOD comment
// Deduct shipping fee only after carrier confirms pickup,
// not at order creation, to avoid race condition with refunds
await deductShippingFee(orderId);
```

---

## 2. Git Workflow & Branching Strategy

### 2.1 Branch Strategy (GitHub Flow)

```
main (production)
├── develop (staging)
│   ├── feature/US-001-product-search
│   ├── feature/US-010-cart
│   ├── fix/order-status-bug
│   └── chore/upgrade-prisma
└── hotfix/payment-webhook-fix (từ main khi cần)
```

### 2.2 Branch Naming

| Loại | Pattern | Ví dụ |
|---|---|---|
| Feature | `feature/<story-id>-<description>` | `feature/US-010-cart-management` |
| Bug fix | `fix/<issue-id>-<description>` | `fix/ORD-123-status-transition` |
| Hotfix | `hotfix/<description>` | `hotfix/payment-callback-null` |
| Chore | `chore/<description>` | `chore/upgrade-dependencies` |
| Release | `release/<version>` | `release/1.2.0` |

### 2.3 Commit Message Convention (Conventional Commits)

```
<type>(<scope>): <subject>

[optional body]

[optional footer]
```

**Types:** `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `perf`

**Ví dụ:**
```
feat(order): add order cancellation endpoint

- Add POST /orders/:id/cancel endpoint
- Validate order status before cancellation
- Release inventory reservation on cancel
- Send cancellation notification email

Closes #123
```

### 2.4 Pull Request Process

1. **Tạo PR** từ feature branch vào `develop`
2. **PR title** theo format: `[US-XXX] Feature description`
3. **PR description** phải có:
   - Summary of changes
   - Testing steps
   - Screenshots (nếu có UI changes)
   - Checklist (tests pass, documentation updated)
4. **Code Review:** Tối thiểu 1 approval từ developer khác (2 cho security-sensitive)
5. **CI checks pass:** Tests, lint, build
6. **Merge strategy:** Squash and merge (giữ history sạch)

### 2.5 Code Review Checklist

- [ ] Logic đúng với requirements
- [ ] No security vulnerabilities (injection, auth bypass, sensitive data leak)
- [ ] Error handling đầy đủ
- [ ] Tests được thêm/cập nhật
- [ ] Performance: không có N+1 queries, không blocking operations
- [ ] No hardcoded secrets hoặc env-specific values
- [ ] API contract không bị breaking change

---

## 3. CI/CD Pipeline

### 3.1 Pipeline Overview

```mermaid
flowchart LR
  PUSH[Git Push\n/ PR] --> CI[CI Pipeline\nGitHub Actions]

  subgraph CI["CI Pipeline"]
    LINT[Lint\nESLint/Prettier]
    TEST[Unit Tests\nJest]
    BUILD[Build\nDocker Image]
    SCAN[Security Scan\nSnyk/CodeQL]
    INT_TEST[Integration Tests\nTestcontainers]
  end

  LINT --> TEST
  TEST --> BUILD
  BUILD --> SCAN
  SCAN --> INT_TEST

  INT_TEST --> STAGING[Deploy to Staging\n(develop branch)]
  STAGING --> E2E[E2E Tests\nPlaywright]
  E2E --> APPROVE{Manual\nApproval}
  APPROVE --> PROD[Deploy to Production\n(main branch)]
```

### 3.2 GitHub Actions Workflow

```yaml
# .github/workflows/ci.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [develop]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check

  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_DB: test_db
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
        options: --health-cmd pg_isready
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      - run: npm ci
      - run: npm run test:unit
      - run: npm run test:integration
      - uses: codecov/codecov-action@v4

  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: github/codeql-action/init@v3
        with:
          languages: typescript
      - uses: github/codeql-action/analyze@v3
      - run: npm audit --audit-level=high

  build:
    needs: [lint, test, security]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: docker/build-push-action@v5
        with:
          push: ${{ github.ref == 'refs/heads/develop' }}
          tags: ghcr.io/${{ github.repository }}:${{ github.sha }}

  deploy-staging:
    needs: build
    if: github.ref == 'refs/heads/develop'
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - name: Deploy to staging
        run: |
          # kubectl set image deployment/app app=ghcr.io/...
          echo "Deploying to staging..."
```

### 3.3 Environment Variables Management

```bash
# .env.example (commit vào repo)
DATABASE_URL=postgresql://user:password@localhost:5432/db
REDIS_URL=redis://localhost:6379
JWT_PRIVATE_KEY=<RSA private key>
VNPAY_TMN_CODE=<from VNPAY>
VNPAY_HASH_SECRET=<from VNPAY>
MOMO_PARTNER_CODE=<from MoMo>
MOMO_ACCESS_KEY=<from MoMo>
MOMO_SECRET_KEY=<from MoMo>
GHN_TOKEN=<from GHN>
GHN_SHOP_ID=<from GHN>

# Tuyệt đối KHÔNG commit .env với giá trị thực vào repo
# Sử dụng GitHub Secrets hoặc AWS Secrets Manager
```

---

## 4. Database Development Guidelines

### 4.1 Migration Rules

```bash
# Tạo migration mới
npx prisma migrate dev --name add_promo_table

# Apply migrations (staging/prod)
npx prisma migrate deploy

# KHÔNG chạy trực tiếp SQL trong production
# Luôn dùng migration scripts
```

### 4.2 Query Performance

```typescript
// ❌ BAD: N+1 query problem
const orders = await prisma.order.findMany();
for (const order of orders) {
  const items = await prisma.orderItem.findMany({ where: { orderId: order.id } });
}

// ✅ GOOD: Eager loading
const orders = await prisma.order.findMany({
  include: {
    items: {
      include: { variant: { include: { product: true } } }
    }
  }
});

// ✅ GOOD: Pagination bắt buộc cho list queries
const orders = await prisma.order.findMany({
  skip: (page - 1) * limit,
  take: limit,
  orderBy: { createdAt: 'desc' }
});
```

---

## 5. API Development Guidelines

### 5.1 RESTful Conventions

```
GET    /products          → List products (paginated)
POST   /products          → Create product
GET    /products/:id      → Get product detail
PUT    /products/:id      → Full update
PATCH  /products/:id      → Partial update
DELETE /products/:id      → Soft delete

GET    /orders            → My orders (customer) / all orders (admin)
POST   /orders            → Create order (checkout)
GET    /orders/:id        → Order detail
POST   /orders/:id/cancel → Cancel order (action, not CRUD)
```

### 5.2 Response Format

```typescript
// Success (list)
{
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8
  }
}

// Success (single)
{
  "data": { ... }
}

// Error
{
  "error": {
    "code": "ORDER_NOT_FOUND",
    "message": "Đơn hàng không tồn tại",
    "details": {},
    "requestId": "uuid"
  }
}
```

### 5.3 Error Code Convention

```
Format: MODULE_ERROR_TYPE
Examples:
  AUTH_INVALID_CREDENTIALS
  AUTH_TOKEN_EXPIRED
  ORDER_NOT_FOUND
  ORDER_INVALID_STATUS_TRANSITION
  INVENTORY_INSUFFICIENT_STOCK
  PAYMENT_GATEWAY_ERROR
  VALIDATION_ERROR
```
