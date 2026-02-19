# Non-Functional Requirements (NFR)

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  
**Chuẩn tham chiếu:** ISO/IEC 25010:2023  

---

## 1. Hiệu năng (Performance)

### 1.1 Core Web Vitals

Toàn bộ trang storefront phải đạt ngưỡng "Good" theo tiêu chuẩn Google:

| Metric | Mục tiêu | Ngưỡng "Good" | Đo trên |
|---|---|---|---|
| **LCP** (Largest Contentful Paint) | < 2.0s | < 2.5s | Mobile P75 |
| **INP** (Interaction to Next Paint) | < 150ms | < 200ms | Mobile P75 |
| **CLS** (Cumulative Layout Shift) | < 0.05 | < 0.1 | Mobile P75 |
| **TTFB** (Time to First Byte) | < 600ms | < 800ms | Mobile P75 |
| **FCP** (First Contentful Paint) | < 1.5s | < 1.8s | Mobile P75 |

### 1.2 API Response Time

| Endpoint nhóm | P50 | P95 | P99 |
|---|---|---|---|
| Product listing / search | < 100ms | < 200ms | < 500ms |
| Product detail | < 80ms | < 150ms | < 300ms |
| Cart operations | < 100ms | < 200ms | < 400ms |
| Checkout / Create order | < 200ms | < 500ms | < 1000ms |
| Admin dashboards | < 300ms | < 800ms | < 2000ms |

*Đo với ≤ 100 concurrent users. Điều kiện: single-instance production.*

### 1.3 Throughput

| Kịch bản | Yêu cầu |
|---|---|
| Lượng truy cập thông thường | 50–200 concurrent users |
| Peak traffic (sale event) | ≥ 500 concurrent users (với auto-scaling) |
| Đơn hàng mỗi ngày (giai đoạn đầu) | 50–500 đơn/ngày |

### 1.4 Chiến lược tối ưu hiệu năng
- **CDN** cho static assets (JS, CSS, images) – CloudFront, Cloudflare, hoặc BunnyCDN
- **Redis cache** cho: product listings, search results, cart sessions (TTL 5–15 phút)
- **Database indexing** trên các trường hay query: product.slug, order.customer_id, inventory.variant_id
- **Image optimization**: WebP format, lazy loading, responsive images (srcset)
- **SSR/SSG** cho trang sản phẩm và danh mục để tối ưu TTFB

---

## 2. Bảo mật (Security)

### 2.1 OWASP Top 10 Compliance

Hệ thống phải giảm thiểu tất cả 10 rủi ro trong OWASP Top 10 Web Application Security Risks 2021:

| # | Rủi ro | Biện pháp |
|---|---|---|
| A01 | Broken Access Control | RBAC nghiêm ngặt; kiểm tra quyền ở mọi endpoint |
| A02 | Cryptographic Failures | TLS 1.2+ cho tất cả kết nối; AES-256 cho dữ liệu nhạy cảm |
| A03 | Injection | Parameterized queries; ORM validation; input sanitization |
| A04 | Insecure Design | Threat modeling trước khi phát triển; security review |
| A05 | Security Misconfiguration | Cấu hình security headers; tắt debug mode production |
| A06 | Vulnerable Components | Dependency scanning; cập nhật định kỳ |
| A07 | Auth & Identity Failures | JWT với expiry ngắn; bcrypt/Argon2 cho passwords |
| A08 | Software Integrity Failures | Signed releases; SBOM tracking |
| A09 | Logging Failures | Audit log đầy đủ; alert khi phát hiện bất thường |
| A10 | SSRF | Whitelist external URLs; firewall egress rules |

### 2.2 OWASP API Security Top 10 (2023)

Áp dụng thêm cho các API endpoint:
- **API1** Broken Object Level Authorization: Kiểm tra ownership trước mọi thao tác
- **API2** Broken Authentication: Không lộ thông tin qua error messages
- **API3** Broken Object Property Level Authorization: Chỉ trả về fields cần thiết (no over-fetching)
- **API4** Unrestricted Resource Consumption: Rate limiting + pagination bắt buộc

### 2.3 Security Headers

Tất cả response HTTP phải bao gồm:
```
Strict-Transport-Security: max-age=31536000; includeSubDomains
Content-Security-Policy: default-src 'self'; ...
X-Frame-Options: DENY
X-Content-Type-Options: nosniff
Referrer-Policy: strict-origin-when-cross-origin
Permissions-Policy: camera=(), microphone=(), geolocation=()
```

### 2.4 PCI DSS Compliance
- **Không lưu** số thẻ, CVV, PIN – chỉ lưu token từ cổng thanh toán
- Kết nối đến cổng thanh toán qua TLS 1.2+ với certificate pinning
- Audit log đầy đủ mọi giao dịch thanh toán (không thể xóa/sửa log)
- Tách biệt network segment cho payment processing

### 2.5 Authentication & Authorization
- **Mật khẩu:** Hash bằng Argon2id (ưu tiên) hoặc bcrypt (cost ≥ 12)
- **JWT:** Access token TTL 15 phút; Refresh token TTL 30 ngày; lưu refresh token hashed trong DB
- **RBAC Roles:** `superadmin | manager | staff | warehouse | customer`
- **2FA:** Tùy chọn cho tài khoản admin (TOTP/OTP qua SMS)
- **Rate limiting auth:** Max 5 failed attempts / 15 phút / IP

### 2.6 WAF & DDoS Protection
- Triển khai WAF (Web Application Firewall) trước API Gateway
- Rate limiting: 100 req/phút cho public API, 1000 req/phút cho authenticated users
- Block IP tự động khi phát hiện bot/scraper

---

## 3. Khả dụng & Độ tin cậy (Availability & Reliability)

### 3.1 SLA Targets

| Môi trường | Uptime target | Planned downtime |
|---|---|---|
| Production | 99.5%/tháng | Max 3.6h/tháng |
| Production (giai đoạn 2+) | 99.9%/tháng | Max 43 phút/tháng |

### 3.2 Recovery Objectives

| Metric | Target |
|---|---|
| **RTO** (Recovery Time Objective) | < 1 giờ cho lỗi nghiêm trọng |
| **RPO** (Recovery Point Objective) | < 1 giờ (backup database mỗi giờ) |

### 3.3 Chiến lược độ tin cậy
- Health check endpoint `/health` và `/ready` cho mỗi service
- Graceful shutdown: xử lý xong requests đang chạy trước khi tắt
- Circuit breaker cho các call đến external services (cổng thanh toán, carrier API)
- Retry với exponential backoff cho webhook processing

---

## 4. Khả năng mở rộng (Scalability)

### 4.1 Horizontal Scaling
- Application tier: auto-scaling theo CPU/memory (≥ 2 instances tối thiểu ở production)
- Database: Read replicas cho query-heavy operations (reporting, listing)
- Cache layer: Redis cluster cho high availability

### 4.2 Data Growth
| Bảng | Ước tính tăng trưởng/năm |
|---|---|
| Products/Variants | 5,000–20,000 records |
| Orders | 10,000–200,000 records/năm |
| Inventory transactions | 50,000–500,000 records/năm |
| Audit logs | 1–5 GB/năm |

### 4.3 Database Partitioning
- Partition bảng `order` và `audit_log` theo `created_at` (range partitioning) khi > 1 triệu records
- Archive orders > 2 năm sang cold storage

---

## 5. SEO & Khả năng tìm kiếm (Discoverability)

### 5.1 Mobile-First Indexing
- Storefront phải responsive, nội dung hiển thị đầy đủ trên mobile (không dùng lazy-reveal ẩn nội dung khỏi Google)
- Viewport meta tag đúng chuẩn
- Touch targets ≥ 48×48px

### 5.2 Structured Data
Trang sản phẩm phải có JSON-LD với schema:
```json
{
  "@type": "Product",
  "name": "...",
  "brand": {"@type": "Brand", "name": "..."},
  "offers": {
    "@type": "Offer",
    "price": "...",
    "priceCurrency": "VND",
    "availability": "https://schema.org/InStock"
  },
  "aggregateRating": {...}
}
```

### 5.3 Technical SEO
- Canonical URL cho trang duplicate (variant colors, phân trang)
- Hreflang nếu có đa ngôn ngữ
- Sitemap.xml tự động cập nhật, tối đa 50,000 URLs/file
- Robots.txt: block `/admin`, `/checkout`, `/api`
- 301 redirect khi đổi URL sản phẩm

### 5.4 Page Speed cho SEO
- Pagespeed Insights (mobile): ≥ 70 điểm
- Tối ưu critical rendering path: inline critical CSS, defer non-critical JS

---

## 6. Khả năng bảo trì (Maintainability)

### 6.1 Code Quality
- Test coverage: ≥ 70% cho business logic (unit tests)
- Code complexity (cyclomatic): ≤ 15 per function
- Linting: ESLint/Biome (frontend), chuẩn PEP8/golint (backend theo ngôn ngữ)

### 6.2 Observability
- Structured logging (JSON format) với correlation ID
- Distributed tracing (OpenTelemetry) cho request flow qua services
- Metrics export (Prometheus format) cho CPU, memory, request rate, error rate

### 6.3 Deployment
- Zero-downtime deployment với rolling update hoặc blue-green
- Rollback trong < 5 phút nếu deployment thất bại
- Môi trường staging phải mirror production về data schema

---

## 7. Khả năng tương thích (Compatibility)

### 7.1 Browser Support
| Browser | Phiên bản tối thiểu |
|---|---|
| Chrome | ≥ 90 |
| Firefox | ≥ 88 |
| Safari | ≥ 14 |
| Samsung Internet | ≥ 14 |
| Edge | ≥ 90 |

### 7.2 Device Support
- Mobile: Màn hình 360px–480px (portrait) – **ưu tiên cao nhất**
- Tablet: 768px–1024px
- Desktop: ≥ 1280px

### 7.3 Network Conditions
- Optimize cho mạng 4G (10–50 Mbps) và 3G (1–5 Mbps)
- Tổng page weight trang sản phẩm: < 2MB (gzip)
- Ảnh sản phẩm: WebP, lazy loading, max 300KB/ảnh

---

## 8. Khả năng tiếp cận (Accessibility)

- WCAG 2.1 Level AA cho các thành phần UI chính
- Contrast ratio ≥ 4.5:1 cho text
- Keyboard navigation cho flow mua hàng chính
- Alt text cho tất cả hình ảnh sản phẩm

---

## 9. Tóm tắt Quality Attributes (ISO/IEC 25010)

| Quality Characteristic | Metric chính | Target |
|---|---|---|
| Performance efficiency | LCP, API P95 | LCP < 2.5s, API < 200ms |
| Security | OWASP compliance | 0 critical/high vulnerabilities |
| Reliability | Uptime | ≥ 99.5%/tháng |
| Maintainability | Test coverage | ≥ 70% |
| Portability | Container-based | Docker + Kubernetes ready |
| Compatibility | Browser support | Chrome/Firefox/Safari ≥ các phiên bản nêu trên |
| Usability | Mobile-first | Lighthouse mobile ≥ 70 |
