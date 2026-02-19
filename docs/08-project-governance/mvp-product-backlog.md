# MVP Product Backlog

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  
**Nguồn:** FRS v1.0.0, Roadmap v1.0.0  
**Phạm vi:** Giai đoạn 1 – MVP (12–16 tuần)

---

## 1. Nhãn ưu tiên

| Nhãn | Ý nghĩa | Sprint target |
|---|---|---|
| **MUST** | Bắt buộc để Go-live; không có thì hệ thống không hoạt động | Giai đoạn 1 (Sprint 0–6) |
| **SHOULD** | Quan trọng nhưng có thể workaround tạm; nên có trước Go-live | Giai đoạn 1 (Sprint 5–6) |
| **LATER** | Mong muốn nhưng không ngăn Go-live | Giai đoạn 2–3 |

**Tiêu chí Done (Definition of Done):**  
✅ API đúng OpenAPI spec, test pass  
✅ UI hiển thị đúng trên mobile + desktop  
✅ Unit test ≥ 80% coverage (business logic)  
✅ Integration test pass (happy path + error cases chính)  
✅ Log/audit đầy đủ (kho/đơn/thanh toán)  
✅ Deploy staging + QA sign-off  
✅ Tài liệu/RTM cập nhật nếu có thay đổi

---

## 2. Epic: Nền tảng kỹ thuật (Sprint 0)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| INFRA-01 | Setup repo FE (Next.js SSR) + repo BE (Node/modular monolith) với cấu trúc module | MUST | 5 | 0 | 🔲 |
| INFRA-02 | Thiết lập CI/CD: lint + test + build + deploy staging tự động | MUST | 5 | 0 | 🔲 |
| INFRA-03 | Cấu hình môi trường dev/staging/prod; tách secret qua env vars | MUST | 3 | 0 | 🔲 |
| INFRA-04 | DB schema migration baseline (PostgreSQL, toàn bộ bảng MVP) | MUST | 5 | 0 | 🔲 |
| INFRA-05 | Thiết lập Redis (cache + session) | MUST | 3 | 0 | 🔲 |
| INFRA-06 | Cấu hình object storage (S3-compatible) cho ảnh sản phẩm | MUST | 3 | 0 | 🔲 |
| INFRA-07 | Wireframe + UI kit (mobile-first) cho toàn bộ luồng MVP | MUST | 8 | 0 | 🔲 |

**Tổng Sprint 0:** 32 SP

---

## 3. Epic: AUTH – Xác thực & Phân quyền (Sprint 1)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| AUTH-01 | Là khách hàng, tôi có thể đăng ký bằng số điện thoại và xác thực OTP để có tài khoản | MUST | 5 | 1 | 🔲 |
| AUTH-02 | Là khách hàng, tôi có thể đăng nhập bằng số điện thoại/mật khẩu | MUST | 3 | 1 | 🔲 |
| AUTH-03 | Là khách hàng, tôi có thể đặt lại mật khẩu qua OTP | MUST | 3 | 1 | 🔲 |
| AUTH-04 | Là Super Admin, tôi có thể cấp/thu hồi role cho nhân viên (Staff/Warehouse/Manager) | MUST | 3 | 1 | 🔲 |
| AUTH-05 | Hệ thống tự động refresh JWT access token; logout thu hồi refresh token | MUST | 3 | 1 | 🔲 |
| AUTH-06 | Hệ thống khóa tài khoản sau 5 lần đăng nhập sai trong 15 phút | MUST | 2 | 1 | 🔲 |
| AUTH-07 | Đăng nhập Google OAuth | LATER | 3 | – | 🔲 |
| AUTH-08 | Đăng nhập bằng email/password | LATER | 2 | – | 🔲 |

**Tổng Sprint 1 (AUTH MUST):** 19 SP

---

## 4. Epic: CATALOG – Sản phẩm & Tìm kiếm (Sprint 1)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| CAT-01 | Là Admin, tôi có thể tạo/sửa/xóa sản phẩm với các biến thể (màu, dung lượng) | MUST | 8 | 1 | 🔲 |
| CAT-02 | Là Admin, tôi có thể upload và sắp xếp hình ảnh sản phẩm | MUST | 3 | 1 | 🔲 |
| CAT-03 | Là khách hàng, tôi có thể tìm kiếm sản phẩm full-text và lọc theo brand/price/storage | MUST | 5 | 1 | 🔲 |
| CAT-04 | Là khách hàng, tôi có thể xem trang chi tiết sản phẩm với giá, ảnh, specs, tình trạng tồn kho | MUST | 5 | 1 | 🔲 |
| CAT-05 | Là Admin, tôi có thể tạo và quản lý cây danh mục sản phẩm | MUST | 3 | 1 | 🔲 |
| CAT-06 | Là Admin, tôi có thể cài đặt SEO metadata (title, description, slug) cho từng sản phẩm | MUST | 3 | 1 | 🔲 |
| CAT-07 | Trang sản phẩm có structured data schema.org/Product cho SEO | MUST | 2 | 1 | 🔲 |

**Tổng Sprint 1 (CATALOG):** 29 SP

---

## 5. Epic: CMS – Banner & Trang tĩnh (Sprint 1)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| CMS-01 | Là Admin, tôi có thể tạo/sửa/xóa banner trang chủ và danh mục | MUST | 3 | 1 | 🔲 |
| CMS-02 | Là Admin, tôi có thể tạo/sửa trang tĩnh (Về chúng tôi, Chính sách bảo hành, Chính sách đổi trả, Bảo mật, Liên hệ) | MUST | 3 | 1 | 🔲 |
| CMS-03 | Hệ thống tự động cập nhật sitemap.xml khi có sản phẩm mới hoặc trang mới | MUST | 2 | 1 | 🔲 |
| CMS-04 | Blog/tin tức hỗ trợ SEO | LATER | 5 | – | 🔲 |

**Tổng Sprint 1 (CMS MUST):** 8 SP

---

## 6. Epic: INVENTORY – Tồn kho (Sprint 2)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| INV-01 | Là Admin, tôi có thể xem tồn kho theo kho/warehouse và biến thể | MUST | 3 | 2 | 🔲 |
| INV-02 | Là Admin, tôi có thể điều chỉnh tồn kho thủ công (nhập/xuất) với lý do; mọi thay đổi được ghi log | MUST | 5 | 2 | 🔲 |
| INV-03 | Là Admin, tôi có thể tạo Purchase Order và nhận hàng để cập nhật tồn kho | MUST | 5 | 2 | 🔲 |
| INV-04 | Hệ thống gửi cảnh báo khi tồn kho biến thể xuống dưới ngưỡng | MUST | 3 | 2 | 🔲 |
| INV-05 | Là Admin, tôi có thể thêm, tra cứu và cập nhật trạng thái IMEI | MUST | 3 | 2 | 🔲 |

**Tổng Sprint 2 (INVENTORY):** 19 SP

---

## 7. Epic: PRICING – Giá & Voucher (Sprint 2)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| PRC-01 | Là Admin, tôi có thể đặt giá sale cho biến thể trong khoảng thời gian; giá sale tự động áp dụng/hết hạn | MUST | 3 | 2 | 🔲 |
| PRC-02 | Là Admin, tôi có thể tạo voucher (% hoặc số tiền cố định) với điều kiện và giới hạn sử dụng | MUST | 5 | 2 | 🔲 |
| PRC-03 | Là khách hàng, tôi có thể nhập mã voucher tại giỏ hàng; chỉ 1 voucher/đơn | MUST | 3 | 2 | 🔲 |
| PRC-04 | Flash sale theo khung giờ | LATER | 5 | – | 🔲 |
| PRC-05 | Quà tặng kèm theo điều kiện đơn | LATER | 5 | – | 🔲 |

**Tổng Sprint 2 (PRICING MUST):** 11 SP

---

## 8. Epic: ORDER – Đơn hàng (Sprint 2–3)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| ORD-01 | Là khách hàng, tôi có thể thêm sản phẩm vào giỏ hàng và cập nhật số lượng | MUST | 3 | 2 | 🔲 |
| ORD-02 | Là khách hàng, tôi có thể điền địa chỉ, chọn phương thức giao hàng và thanh toán để đặt hàng | MUST | 8 | 2 | 🔲 |
| ORD-03 | Là khách hàng, tôi nhận email xác nhận đơn hàng trong < 60 giây sau khi đặt | MUST | 3 | 3 | 🔲 |
| ORD-04 | Là Admin, tôi có thể xem danh sách đơn hàng và lọc theo trạng thái/ngày/khách | MUST | 3 | 3 | 🔲 |
| ORD-05 | Là Admin, tôi có thể cập nhật trạng thái đơn (Xác nhận → Đóng gói → Giao vận chuyển) | MUST | 5 | 3 | 🔲 |
| ORD-06 | Là khách hàng/Admin, tôi có thể huỷ đơn (theo rule phân quyền); tồn kho được hoàn lại | MUST | 3 | 3 | 🔲 |
| ORD-07 | Trang theo dõi đơn hàng hiển thị lịch sử trạng thái đầy đủ với timestamp | MUST | 3 | 3 | 🔲 |
| ORD-08 | Hệ thống gửi SMS/email khi trạng thái đơn thay đổi | MUST | 3 | 3 | 🔲 |

**Tổng Sprint 2–3 (ORDER):** 31 SP

---

## 9. Epic: PAYMENT – Thanh toán (Sprint 3)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| PAY-01 | Là khách hàng, tôi có thể thanh toán online qua VNPAY | MUST | 8 | 3 | 🔲 |
| PAY-02 | Là khách hàng, tôi có thể thanh toán online qua MoMo | MUST | 5 | 3 | 🔲 |
| PAY-03 | Là khách hàng, tôi có thể thanh toán COD | MUST | 3 | 3 | 🔲 |
| PAY-04 | Hệ thống xử lý webhook từ cổng thanh toán, xác minh chữ ký HMAC-SHA256, cập nhật đơn | MUST | 5 | 3 | 🔲 |
| PAY-05 | Là Admin, tôi có thể yêu cầu hoàn tiền cho đơn hàng; hệ thống ghi nhận refund log | MUST | 5 | 3 | 🔲 |
| PAY-06 | Thanh toán qua ZaloPay | SHOULD | 3 | 4 | 🔲 |
| PAY-07 | Trả góp qua đối tác BNPL (Kredivo/Home Pay) | LATER | 5 | – | 🔲 |

**Tổng Sprint 3 (PAYMENT MUST):** 26 SP  
**Sprint 4 (PAYMENT SHOULD):** 3 SP

---

## 10. Epic: SHIPPING – Giao vận (Sprint 3–4)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| SHIP-01 | Hệ thống tính phí ship qua API GHN/GHTK theo địa chỉ và cân nặng | MUST | 3 | 3 | 🔲 |
| SHIP-02 | Là Admin, tôi có thể tạo vận đơn GHN/GHTK và in nhãn | MUST | 5 | 4 | 🔲 |
| SHIP-03 | Là khách hàng, tôi có thể theo dõi trạng thái vận chuyển real-time | MUST | 3 | 4 | 🔲 |
| SHIP-04 | Hệ thống nhận webhook từ carrier và cập nhật trạng thái vận chuyển + đơn hàng trong < 30s | MUST | 5 | 4 | 🔲 |
| SHIP-05 | Là Admin, tôi có thể thay đổi địa chỉ giao hàng trước khi tạo vận đơn | SHOULD | 2 | 5 | 🔲 |

**Tổng Sprint 3–4 (SHIPPING MUST):** 16 SP

---

## 11. Epic: CUSTOMER SERVICE – CSKH & Bảo hành (Sprint 4–5)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| CS-01 | Là khách hàng, tôi có thể tạo yêu cầu đổi/trả/bảo hành trong cửa sổ đổi trả 7 ngày | MUST | 5 | 4 | 🔲 |
| CS-02 | Là Staff, tôi có thể xử lý ticket đổi trả, cập nhật trạng thái và ghi chú | MUST | 5 | 4 | 🔲 |
| CS-03 | Là Admin, tôi có thể tra cứu lịch sử bảo hành theo IMEI | MUST | 3 | 5 | 🔲 |
| CS-04 | Là Admin, tôi có thể cấu hình chính sách đổi trả (số ngày, điều kiện) | MUST | 2 | 5 | 🔲 |

**Tổng Sprint 4–5 (CS):** 15 SP

---

## 12. Epic: REPORTING – Báo cáo (Sprint 5)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| RPT-01 | Là Manager, tôi có thể xem báo cáo doanh thu theo ngày/tuần/tháng và xuất Excel/CSV | MUST | 5 | 5 | 🔲 |
| RPT-02 | Là Manager, tôi có thể xem báo cáo đơn hàng theo trạng thái và xuất Excel/CSV | MUST | 3 | 5 | 🔲 |
| RPT-03 | Là Warehouse, tôi có thể xem báo cáo tồn kho hiện tại và xuất Excel/CSV | MUST | 3 | 5 | 🔲 |
| RPT-04 | Là Manager, tôi có thể xem và xuất báo cáo đối soát COD | MUST | 3 | 5 | 🔲 |
| RPT-05 | Là Manager, tôi có thể xem và xuất báo cáo đối soát thanh toán online | MUST | 3 | 5 | 🔲 |

**Tổng Sprint 5 (REPORTING):** 17 SP

---

## 13. Epic: Security & Hardening (Sprint 5–6)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| SEC-01 | Toàn bộ endpoint có auth check; RBAC kiểm tra quyền ownership trước mọi thao tác | MUST | 5 | 5 | 🔲 |
| SEC-02 | Input validation và sanitization trên toàn bộ API (OWASP A03) | MUST | 3 | 5 | 🔲 |
| SEC-03 | Rate limiting: 100 req/phút public, 1000 req/phút authenticated | MUST | 3 | 5 | 🔲 |
| SEC-04 | Security headers (HSTS, CSP, X-Frame-Options, etc.) | MUST | 2 | 5 | 🔲 |
| SEC-05 | Audit log bất biến cho payment/inventory/order | MUST | 3 | 5 | 🔲 |
| SEC-06 | Dependency scanning + secret detection trong CI | MUST | 2 | 6 | 🔲 |
| SEC-07 | WAF cơ bản (Cloudflare) | SHOULD | 2 | 6 | 🔲 |
| SEC-08 | Basic penetration test checklist | SHOULD | 3 | 6 | 🔲 |

**Tổng Sprint 5–6 (Security):** 23 SP

---

## 14. Epic: QA & Performance (Sprint 6)

| ID | User Story | Nhãn | SP | Sprint | Status |
|---|---|---|---|---|---|
| QA-01 | E2E test: luồng mua hàng đầu đủ (browse → cart → checkout → payment → confirm) | MUST | 5 | 6 | 🔲 |
| QA-02 | E2E test: luồng đổi trả + hoàn tiền | MUST | 3 | 6 | 🔲 |
| QA-03 | E2E test: tạo vận đơn + tracking | MUST | 3 | 6 | 🔲 |
| QA-04 | Load test: API chính đạt P95 < 500ms với 100 concurrent users | MUST | 3 | 6 | 🔲 |
| QA-05 | Storefront đạt Core Web Vitals "Good": LCP < 2.5s, INP < 200ms, CLS < 0.1 | MUST | 5 | 6 | 🔲 |
| QA-06 | UAT với stakeholders; sign-off | MUST | 3 | 6 | 🔲 |

**Tổng Sprint 6 (QA):** 22 SP

---

## 15. Tổng quan Sprint Plan (MVP)

| Sprint | Tuần | Epic | Story Points | Tích lũy |
|---|---|---|---|---|
| Sprint 0 | 1–2 | Nền tảng, Wireframe, DB schema | 32 | 32 |
| Sprint 1 | 3–4 | Auth, Catalog, CMS | 56 | 88 |
| Sprint 2 | 5–6 | Inventory, Pricing, Cart/Order (khởi tạo) | 49 | 137 |
| Sprint 3 | 7–8 | Order (hoàn thiện), Payment, Shipping (phí) | 62 | 199 |
| Sprint 4 | 9–10 | Shipping (vận đơn/webhook), Admin Backoffice, CS | 34 | 233 |
| Sprint 5 | 11–12 | CS (tiếp), Reporting, Security & Hardening | 35 | 268 |
| Sprint 6 | 13–14 | QA, Performance, UAT, Go-live prep | 22 | 290 |

**Tổng Story Points MVP:** ~290 SP  
**Velocity giả định:** 40–45 SP/sprint (team 6–9 người)  
**Thời gian dự kiến:** 12–14 tuần (6–7 sprint × 2 tuần)

---

## 16. Backlog – Giai đoạn 2 (LATER)

| ID | Tính năng | Nhãn | Sprint giai đoạn 2 |
|---|---|---|---|
| P2-01 | Flash sale theo khung giờ | LATER | G2-S1 |
| P2-02 | Quà tặng kèm theo điều kiện | LATER | G2-S1 |
| P2-03 | Combo/bundle sản phẩm | LATER | G2-S1 |
| P2-04 | ZaloPay (nếu chưa xong MVP) | LATER | G2-S1 |
| P2-05 | Báo cáo đối soát COD nâng cao | LATER | G2-S2 |
| P2-06 | Trade-in cơ bản | LATER | G2-S3 |
| P2-07 | Tích hợp POS (KiotViet/Sapo) | LATER | G2-S4 |
| P2-08 | Loyalty/CRM (điểm tích lũy, hạng) | LATER | G3-S1 |
| P2-09 | Blog/Content Marketing | LATER | G3-S2 |
| P2-10 | Mobile app | LATER | G3-S4 |
| P2-11 | Google OAuth | LATER | G2-S1 |
| P2-12 | BNPL (trả góp Kredivo/Home Pay) | LATER | G2-S2 |
| P2-13 | Chuyển kho giữa chi nhánh | LATER | G2-S2 |
| P2-14 | SEO nâng cao (schema markup đầy đủ) | LATER | G3-S1 |

---

*Backlog này được cập nhật cuối mỗi sprint (Sprint Review) bởi Product Owner. Mọi thay đổi phạm vi MUST phải qua Change Management ([change-management.md](../07-project-management/change-management.md)).*
