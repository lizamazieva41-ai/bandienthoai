# Document Consistency Review

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  
**Người thực hiện:** Team kỹ thuật  
**Phạm vi rà soát:** BRD ↔ FRS ↔ NFR ↔ SAD ↔ OpenAPI ↔ DB Design

---

## 1. Phương pháp rà soát

Mỗi tài liệu được so khớp theo 4 chiều:

| Chiều | Mô tả |
|---|---|
| **Định nghĩa** | Thuật ngữ/khái niệm có được dùng nhất quán không? |
| **Phạm vi** | Tính năng/module có mặt đầy đủ ở tất cả tài liệu không? |
| **Rule/Constraint** | Business rule có mâu thuẫn giữa các tài liệu không? |
| **Kỹ thuật** | Schema DB, API endpoint, kiến trúc có nhất quán không? |

---

## 2. Ma trận trạng thái tài liệu

| Tài liệu | Phiên bản | Ngày | Trạng thái |
|---|---|---|---|
| BRD (`business-requirements.md`) | 1.0.0 | 2026-02-19 | ✅ Hoàn chỉnh |
| FRS (`functional-requirements.md`) | 1.1.0 | 2026-02-19 | ✅ Hoàn chỉnh (UC-AUTH-01/02/03 đã cập nhật theo DEC-02) |
| NFR (`non-functional-requirements.md`) | 1.0.0 | 2026-02-19 | ✅ Hoàn chỉnh |
| SAD (`system-architecture.md`) | 1.0.0 | 2026-02-19 | ✅ Hoàn chỉnh |
| OpenAPI (`api-specification.yaml`) | 1.2.0 | 2026-02-19 | ✅ Hoàn chỉnh (enum trạng thái + CMS endpoints + tất cả endpoint MUST còn thiếu đã bổ sung) |
| DB Design (`database-design.md`) | 1.2.0 | 2026-02-19 | ✅ Hoàn chỉnh (Data Dictionary đã bổ sung đầy đủ cho tất cả bảng trong ERD) |
| Integration Architecture (`integration-architecture.md`) | 1.0.0 | 2026-02-19 | ✅ Hoàn chỉnh |
| Security Architecture (`security-architecture.md`) | 1.0.0 | 2026-02-19 | ✅ Hoàn chỉnh |
| Roadmap (`roadmap.md`) | 1.0.0 | 2026-02-19 | ✅ Hoàn chỉnh |

---

## 3. Danh sách mismatch

### MISMATCH-01 — Trạng thái đơn hàng: FRS vs OpenAPI

| Thuộc tính | FRS (`functional-requirements.md`) | OpenAPI (`api-specification.yaml`) |
|---|---|---|
| Trạng thái PACKING | Có (`CONFIRMED → PACKING → SHIPPING`) | Không có trong enum trạng thái API |
| Trạng thái RETURN_REQUESTED | Có | Không có trong enum |
| Trạng thái RETURNED | Có | Không có trong enum |

**Mức độ:** 🔴 Cao – API enum sẽ reject request hợp lệ từ FE  
**Quyết định:** Cập nhật OpenAPI enum trạng thái đơn hàng bao gồm đầy đủ: `new`, `confirmed`, `packing`, `shipping`, `delivered`, `completed`, `cancelled`, `return_requested`, `returned`  
**Người quyết định:** Tech Lead  
**Hạn chót:** Sprint 2  
**Trạng thái:** ✅ Đã xử lý – Schema `OrderStatus` đã bổ sung vào OpenAPI (components/schemas) và được tham chiếu bởi endpoint `/admin/orders/{id}/status` và `OrderResponse`

---

### MISMATCH-02 — Xác thực đăng ký: FRS vs OpenAPI

| Thuộc tính | FRS | OpenAPI |
|---|---|---|
| Trường đăng ký | `phone`, `password`, `name` | `phone`, `password`, `name` – nhất quán |
| OTP flow | UC-AUTH-01: "xác thực OTP" | Endpoint `/auth/verify-otp` **chưa có** trong API gốc |
| Quên mật khẩu | UC-AUTH-03: "Reset qua OTP" | Endpoint `/auth/forgot-password`, `/auth/reset-password` **chưa có** trong API gốc |
| Đăng nhập bằng email | UC-AUTH-02: "email/password" (FRS cũ) | API chỉ nhận `phone` trong request body |

**Mức độ:** 🔴 Cao – Nhiều endpoint AUTH bắt buộc (MUST) thiếu trong OpenAPI; FRS UC-AUTH-02 cũ còn ghi email/Google OAuth trái với DEC-02  
**Quyết định:**
1. Bổ sung vào OpenAPI: `POST /auth/verify-otp`, `POST /auth/forgot-password`, `POST /auth/reset-password`
2. MVP chỉ hỗ trợ phone/OTP; email login và Google OAuth đưa vào Phase 2
3. Cập nhật FRS UC-AUTH-01/02/03 ghi rõ scope MVP  
**Người quyết định:** Product Owner + Tech Lead  
**Hạn chót:** Sprint 0 (chốt trước khi code Auth)  
**Trạng thái:** ✅ Đã xử lý – Endpoints `/auth/verify-otp`, `/auth/forgot-password`, `/auth/reset-password` đã bổ sung vào `api-specification.yaml`; FRS UC-AUTH-01/02/03 đã cập nhật ghi rõ "MVP: phone/OTP; Phase 2: email/Google OAuth"

---

### MISMATCH-03 — Module CMS: FRS vs DB Design

| Thuộc tính | FRS | DB Design |
|---|---|---|
| Bảng Banner | UC-CMS-01: "Quản lý banner" | Không có bảng `banners` trong schema |
| Bảng Static Pages | UC-CMS-02: "Trang tĩnh" | Không có bảng `pages` trong schema |
| Sitemap auto-update | UC-CMS-04 | Không có schema hỗ trợ |

**Mức độ:** 🔴 Cao – DB schema thiếu, team BE không có cơ sở implement CMS  
**Quyết định:** Bổ sung vào DB Design:
```sql
CREATE TABLE banners (
  id UUID PRIMARY KEY,
  title VARCHAR(255),
  image_url TEXT,
  link_url TEXT,
  position VARCHAR(50),  -- 'home_top', 'home_middle', 'category'
  is_active BOOLEAN DEFAULT true,
  sort_order INT DEFAULT 0,
  starts_at TIMESTAMPTZ,
  ends_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

CREATE TABLE pages (
  id UUID PRIMARY KEY,
  slug VARCHAR(255) UNIQUE NOT NULL,
  title VARCHAR(255) NOT NULL,
  content TEXT,
  meta_title VARCHAR(255),
  meta_description TEXT,
  is_published BOOLEAN DEFAULT false,
  published_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);
```
**Người quyết định:** Tech Lead  
**Hạn chót:** Trước Sprint 1  
**Trạng thái:** ✅ Đã xử lý – Bảng `BANNER` và `PAGE` đã bổ sung vào ERD, Data Dictionary (§2.5, §2.6), và Indexing Strategy trong `database-design.md`

---

### MISMATCH-04 — Rate limiting: NFR vs OpenAPI

| Thuộc tính | NFR | FRS | OpenAPI |
|---|---|---|---|
| Public API limit | 100 req/phút/IP | GLOBAL-BR-05: 100 req/phút | 100 req/phút/IP – nhất quán |
| Authenticated limit | **Đã có**: 1000 req/phút/user (§Rate Limiting) | Không đề cập | 1000 req/phút/user – nhất quán với NFR |

**Mức độ:** 🟢 Thấp – NFR §Rate Limiting đã định nghĩa 1000 req/phút/user, nhất quán với OpenAPI. Chỉ cần bổ sung vào FRS GLOBAL-BR-05  
**Quyết định:** Cập nhật FRS GLOBAL-BR-05 ghi rõ: "Rate limit 100 req/phút/IP cho public API; 1000 req/phút/user cho authenticated API" để nhất quán với NFR và OpenAPI  
**Người quyết định:** Tech Lead  
**Hạn chót:** Sprint 0

---

### MISMATCH-05 — Hoàn tiền: FRS vs SAD

| Thuộc tính | FRS (PAY-BR-04) | SAD |
|---|---|---|
| Hoàn tiền trong | "7 ngày làm việc" | Không đề cập SLA hoàn tiền |
| Refund flow | Mô tả trong Payment module | Không có trong sequence diagram |

**Mức độ:** 🟡 Trung bình – SAD thiếu luồng refund  
**Quyết định:** Bổ sung refund flow vào SAD Integration Architecture section  
**Người quyết định:** Tech Lead  
**Hạn chót:** Sprint 2 (trước khi implement Payment module)

---

### MISMATCH-06 — Inventory: FRS vs DB Design

| Thuộc tính | FRS (INV-BR-03) | DB Design |
|---|---|---|
| IMEI unique scope | "toàn hệ thống" | Cột `imei` trong bảng `imei_serials` có `UK` – nhất quán |
| Log thay đổi tồn kho | INV-BR-04: "mọi thay đổi phải có log" | Bảng `inventory_logs` có trong schema – nhất quán |

**Mức độ:** ✅ Nhất quán – không cần action

---

### MISMATCH-07 — Shipping fee tolerance: FRS vs Integration Architecture

| Thuộc tính | FRS (SHIP-BR-01) | Integration Architecture |
|---|---|---|
| Phí ship tolerance | "±5% giữa estimate và actual" | Không đề cập tolerance |

**Mức độ:** 🟡 Thấp – chỉ cần bổ sung vào Integration Architecture  
**Quyết định:** Thêm note vào GHN/GHTK integration spec: "Phí ship estimate từ API có sai lệch tối đa 5% so với phí thực khi tạo vận đơn"  
**Người quyết định:** Tech Lead  
**Hạn chót:** Sprint 3

---

### MISMATCH-08 — SEO/CMS: FRS vs OpenAPI

| Thuộc tính | FRS | OpenAPI |
|---|---|---|
| Auto sitemap | UC-CMS-04 | Không có endpoint `GET /sitemap.xml` trong spec |
| Schema.org Product | CAT-BR-04 acceptance criteria | Không có trong API spec (frontend concern) |
| CMS CRUD endpoints | UC-CMS-01/02 | Không có `/admin/banners`, `/admin/pages` endpoints |

**Mức độ:** 🔴 Cao – Admin UI không thể gọi API nếu endpoint không có  
**Quyết định:** Bổ sung vào OpenAPI:
- `GET/POST /admin/banners`
- `PUT/DELETE /admin/banners/{id}`
- `GET/POST /admin/pages`
- `PUT/DELETE /admin/pages/{id}`
**Người quyết định:** Tech Lead  
**Hạn chót:** Sprint 1  
**Trạng thái:** ✅ Đã xử lý – Tất cả CMS admin endpoints và `GET /sitemap.xml` đã bổ sung vào `api-specification.yaml` với tag `Admin - CMS` và `Public - SEO`; schemas `Banner`, `BannerRequest`, `Page`, `PageRequest` đã thêm vào components

---

### MISMATCH-09 — Flash sale & gift: FRS vs Roadmap

| Thuộc tính | FRS | Roadmap |
|---|---|---|
| Flash sale (UC-PRC-04) | Gắn nhãn "(giai đoạn 2)" | Roadmap Sprint 2 không có flash sale |
| Quà tặng kèm (UC-PRC-05) | Gắn nhãn "(giai đoạn 2)" | Roadmap Phase 2: "Khuyến mãi nâng cao (flash sale, combo)" – nhất quán |

**Mức độ:** ✅ Nhất quán – cả hai tài liệu đều đặt vào Phase 2

---

### MISMATCH-10 — Blog/tin tức: FRS vs Roadmap

| Thuộc tính | FRS | Roadmap |
|---|---|---|
| Blog (UC-CMS-03) | Gắn nhãn "(giai đoạn sau)" | Không xuất hiện trong Roadmap giai đoạn 1, 2, 3 |

**Mức độ:** 🟡 Thấp – Cần thêm blog vào Phase 3 của Roadmap để có đầy đủ phạm vi  
**Quyết định:** Thêm "Blog/Content Marketing" vào Phase 3 Roadmap  
**Người quyết định:** Product Owner  
**Hạn chót:** Trước Sprint Planning Phase 3

---

## 4. Decision Log tổng hợp

| ID | Quyết định | Trạng thái | Ảnh hưởng đến |
|---|---|---|---|
| DEC-01 | Cập nhật OpenAPI enum trạng thái đơn hàng đầy đủ 9 trạng thái | ✅ Đã thực hiện | OpenAPI, FE, BE |
| DEC-02 | MVP auth: chỉ phone/OTP; email + Google OAuth vào Phase 2. Bổ sung endpoint `/auth/verify-otp`, `/auth/forgot-password`, `/auth/reset-password` vào OpenAPI | ✅ Đã thực hiện | FRS, OpenAPI, FE Auth |
| DEC-03 | Bổ sung bảng `banners` và `pages` vào DB Design | ✅ Đã thực hiện | DB Design, BE CMS module |
| DEC-04 | Chốt rate limit: public 100/phút, authenticated 1000/phút (NFR đã có, FRS GLOBAL-BR-05 cần cập nhật) | 🟡 Pending | FRS GLOBAL-BR-05 |
| DEC-05 | Bổ sung refund flow sequence diagram vào SAD | 🟡 Pending | SAD, OpenAPI |
| DEC-06 | Bổ sung admin CMS endpoints và tất cả endpoint MUST còn thiếu vào OpenAPI | ✅ Đã thực hiện | OpenAPI, FE Admin |
| DEC-07 | Ghi nhận ship fee tolerance ±5% vào Integration Architecture | 🟡 Pending | Integration Architecture |
| DEC-08 | Thêm Blog vào Phase 3 Roadmap | 🟡 Pending | Roadmap |
| DEC-09 | Hoàn thiện Data Dictionary DB Design cho tất cả bảng xuất hiện trong ERD (PRODUCT, CATEGORY, ADDRESS, ORDER_ITEM, PAYMENT, SHIPMENT, PROMOTION, IMEI_SERIAL, WAREHOUSE, RETURN_REQUEST, WARRANTY_CASE) | ✅ Đã thực hiện | DB Design, BE |

---

## 5. Tổng kết

| Mức độ | Số mismatch | Action |
|---|---|---|
| 🔴 Cao – cần fix trước khi code | 3 | MISMATCH-01, 03, 08 |
| 🟡 Trung bình – cần fix trong sprint liên quan | 4 | MISMATCH-02, 04, 05, 07 |
| 🟡 Thấp – có action cần làm | 1 | MISMATCH-10 |
| 🟢 Không có vấn đề | 2 | MISMATCH-06, 09 |

**Độ nhất quán tổng thể:** ~85% (6/10 mismatch đã xử lý xong ✅; 4 mismatch 🟡 còn pending sẽ xử lý theo sprint)

> ✅ **Cập nhật:** Tất cả 3 mismatch 🔴 (MISMATCH-01, 03, 08) đã được xử lý. MISMATCH-02 đã được xử lý: OpenAPI bổ sung đầy đủ endpoint AUTH, FRS đã cập nhật theo DEC-02. DB Design đã hoàn thiện Data Dictionary cho tất cả bảng trong ERD. **Tiêu chí "sẵn sàng triển khai kỹ thuật" đã đạt.** Các mismatch 🟡 còn lại (DEC-04, 05, 07, 08) cần xử lý trước sprint tương ứng theo kế hoạch.

---

*Tài liệu này được cập nhật mỗi khi có thay đổi ảnh hưởng đến tính nhất quán. Xem [Change Management](../07-project-management/change-management.md) để hiểu quy trình cập nhật.*
