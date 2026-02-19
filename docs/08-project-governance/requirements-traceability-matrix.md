# Requirements Traceability Matrix (RTM)

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  
**Chuẩn tham chiếu:** ISO/IEC/IEEE 29148:2018  

---

## 1. Hướng dẫn đọc

Mỗi dòng trong RTM ánh xạ một Use Case từ FRS sang:
- **Screen/UI** – màn hình/component FE liên quan
- **API Endpoint** – endpoint trong OpenAPI spec
- **DB Table(s)** – bảng DB liên quan
- **Test Case ID** – test case tương ứng trong test plan
- **Priority** – MUST (MVP) | SHOULD (Phase 1+) | LATER (Phase 2–3)
- **Status** – 🔲 Pending | 🔄 In Progress | ✅ Done

---

## 2. Module AUTH – Xác thực & Phân quyền

| UC ID | Use Case | Screen/UI | API Endpoint | DB Table(s) | Test Case ID | Priority | Status |
|---|---|---|---|---|---|---|---|
| UC-AUTH-01 | Đăng ký tài khoản | `RegisterPage` | `POST /auth/register`, `POST /auth/verify-otp` | `customers` | TC-AUTH-01, TC-AUTH-02 | MUST | 🔲 |
| UC-AUTH-02 | Đăng nhập (phone/password) | `LoginPage` | `POST /auth/login` | `customers` | TC-AUTH-03 | MUST | 🔲 |
| UC-AUTH-03 | Quên mật khẩu | `ForgotPasswordPage` | `POST /auth/forgot-password`, `POST /auth/reset-password` | `customers` | TC-AUTH-04, TC-AUTH-05 | MUST | 🔲 |
| UC-AUTH-04 | Phân quyền admin | `AdminRolePage` | `GET /admin/staff`, `PUT /admin/staff/{id}/role` | `staff_accounts`, `roles` | TC-AUTH-06 | MUST | 🔲 |
| UC-AUTH-05 | Quản lý phiên (JWT/refresh) | *(middleware)* | `POST /auth/refresh`, `POST /auth/logout` | `refresh_tokens` | TC-AUTH-07, TC-AUTH-08 | MUST | 🔲 |

**Coverage AUTH:** 5/5 use cases = **100%**

---

## 3. Module CATALOG – Sản phẩm & Tìm kiếm

| UC ID | Use Case | Screen/UI | API Endpoint | DB Table(s) | Test Case ID | Priority | Status |
|---|---|---|---|---|---|---|---|
| UC-CAT-01 | Tạo/sửa sản phẩm | `AdminProductForm` | `POST /admin/products`, `PUT /admin/products/{id}` | `products`, `product_variants` | TC-CAT-01, TC-CAT-02 | MUST | 🔲 |
| UC-CAT-02 | Quản lý hình ảnh | `AdminProductImages` | `POST /admin/products/{id}/images`, `DELETE /admin/products/{id}/images/{imageId}` | `product_images` | TC-CAT-03 | MUST | 🔲 |
| UC-CAT-03 | Tìm kiếm sản phẩm | `SearchPage`, `CategoryPage` | `GET /products?q=&brand=&price_min=&price_max=` | `products`, `product_variants` | TC-CAT-04, TC-CAT-05 | MUST | 🔲 |
| UC-CAT-04 | Xem chi tiết sản phẩm | `ProductDetailPage` | `GET /products/{slug}` | `products`, `product_variants`, `inventory` | TC-CAT-06 | MUST | 🔲 |
| UC-CAT-05 | Quản lý danh mục | `AdminCategoryPage` | `GET/POST /admin/categories`, `PUT/DELETE /admin/categories/{id}` | `categories`, `product_categories` | TC-CAT-07 | MUST | 🔲 |
| UC-CAT-06 | SEO metadata | `AdminProductSEO` | `PUT /admin/products/{id}` (field: seo) | `products` (slug, meta_title, meta_desc) | TC-CAT-08 | MUST | 🔲 |

**Coverage CATALOG:** 6/6 use cases = **100%**

---

## 4. Module PRICING – Giá & Khuyến mãi

| UC ID | Use Case | Screen/UI | API Endpoint | DB Table(s) | Test Case ID | Priority | Status |
|---|---|---|---|---|---|---|---|
| UC-PRC-01 | Tạo sale price | `AdminVariantPricing` | `PUT /admin/products/{id}/variants/{variantId}` | `product_variants` (sale_price, sale_starts_at, sale_ends_at) | TC-PRC-01 | MUST | 🔲 |
| UC-PRC-02 | Tạo voucher | `AdminVoucherForm` | `POST /admin/promotions/vouchers` | `promotions`, `vouchers` | TC-PRC-02, TC-PRC-03 | MUST | 🔲 |
| UC-PRC-03 | Áp voucher | `CartPage`, `CheckoutPage` | `POST /cart/apply-voucher` | `promotions`, `order_promotions` | TC-PRC-04, TC-PRC-05 | MUST | 🔲 |
| UC-PRC-04 | Flash sale | `AdminFlashSalePage` | `POST /admin/promotions/flash-sales` | `promotions` | TC-PRC-06 | LATER | 🔲 |
| UC-PRC-05 | Quà tặng kèm | `AdminGiftRulePage` | `POST /admin/promotions/gift-rules` | `promotions` | TC-PRC-07 | LATER | 🔲 |

**Coverage PRICING (MVP):** 3/3 MUST use cases = **100%**  
**Coverage PRICING (All):** 3/5 implemented = **60%** (2 LATER items excluded from MVP)

---

## 5. Module INVENTORY – Tồn kho

| UC ID | Use Case | Screen/UI | API Endpoint | DB Table(s) | Test Case ID | Priority | Status |
|---|---|---|---|---|---|---|---|
| UC-INV-01 | Xem tồn kho | `AdminInventoryPage` | `GET /admin/inventory` | `inventory`, `warehouses` | TC-INV-01 | MUST | 🔲 |
| UC-INV-02 | Điều chỉnh tồn kho | `AdminInventoryAdjust` | `POST /admin/inventory/adjustments` | `inventory`, `inventory_logs` | TC-INV-02, TC-INV-03 | MUST | 🔲 |
| UC-INV-03 | Nhập hàng từ PO | `AdminPurchaseOrderPage` | `POST /admin/purchase-orders`, `POST /admin/purchase-orders/{id}/receive` | `purchase_orders`, `purchase_items`, `inventory` | TC-INV-04 | MUST | 🔲 |
| UC-INV-04 | Cảnh báo tồn thấp | *(email/notification)* | *(internal cron)* | `inventory`, `notification_settings` | TC-INV-05 | MUST | 🔲 |
| UC-INV-05 | Quản lý IMEI | `AdminIMEIPage` | `GET /admin/inventory/imei`, `POST /admin/inventory/imei` | `imei_serials` | TC-INV-06, TC-INV-07 | MUST | 🔲 |

**Coverage INVENTORY:** 5/5 use cases = **100%**

---

## 6. Module ORDER – Đơn hàng

| UC ID | Use Case | Screen/UI | API Endpoint | DB Table(s) | Test Case ID | Priority | Status |
|---|---|---|---|---|---|---|---|
| UC-ORD-01 | Tạo đơn hàng | `CheckoutPage` | `POST /orders` | `orders`, `order_items`, `inventory` | TC-ORD-01, TC-ORD-02 | MUST | 🔲 |
| UC-ORD-02 | Xem đơn hàng (khách) | `AccountOrdersPage`, `OrderDetailPage` | `GET /account/orders`, `GET /account/orders/{id}` | `orders`, `order_items`, `shipments` | TC-ORD-03 | MUST | 🔲 |
| UC-ORD-03 | Quản lý đơn (admin) | `AdminOrdersPage`, `AdminOrderDetail` | `GET /admin/orders`, `PUT /admin/orders/{id}/status` | `orders`, `order_status_logs` | TC-ORD-04, TC-ORD-05 | MUST | 🔲 |
| UC-ORD-04 | Huỷ đơn | `OrderDetailPage` (nút huỷ), `AdminOrderDetail` | `PUT /admin/orders/{id}/cancel` | `orders`, `order_status_logs`, `inventory` | TC-ORD-06 | MUST | 🔲 |
| UC-ORD-05 | Lịch sử trạng thái | `OrderDetailPage` (timeline) | `GET /account/orders/{id}` (field: status_history) | `order_status_logs` | TC-ORD-07 | MUST | 🔲 |
| UC-ORD-06 | Email/SMS thông báo | *(background job)* | *(internal event)* | `orders`, `notification_logs` | TC-ORD-08 | MUST | 🔲 |

**Coverage ORDER:** 6/6 use cases = **100%**

---

## 7. Module PAYMENT – Thanh toán

| UC ID | Use Case | Screen/UI | API Endpoint | DB Table(s) | Test Case ID | Priority | Status |
|---|---|---|---|---|---|---|---|
| UC-PAY-01 | Tạo payment intent | `CheckoutPage` | `POST /payments/initiate` | `payments` | TC-PAY-01 | MUST | 🔲 |
| UC-PAY-02 | Redirect tới gateway | `CheckoutPage` (redirect) | *(redirect URL from gateway)* | `payments` | TC-PAY-02 | MUST | 🔲 |
| UC-PAY-03 | Xử lý webhook thanh toán | *(background handler)* | `POST /webhooks/payment` | `payments`, `orders` | TC-PAY-03, TC-PAY-04 | MUST | 🔲 |
| UC-PAY-04 | Hoàn tiền (refund) | `AdminOrderDetail` (nút refund) | `POST /admin/orders/{id}/refund` | `payments`, `payment_refunds` | TC-PAY-05 | MUST | 🔲 |
| UC-PAY-05 | Đối soát giao dịch | `AdminReportsCOD` | `GET /admin/reports/payment-reconciliation` | `payments` | TC-PAY-06 | MUST | 🔲 |

**Coverage PAYMENT:** 5/5 use cases = **100%**

---

## 8. Module SHIPPING – Giao vận

| UC ID | Use Case | Screen/UI | API Endpoint | DB Table(s) | Test Case ID | Priority | Status |
|---|---|---|---|---|---|---|---|
| UC-SHIP-01 | Tính phí ship | `CheckoutPage` | `POST /shipping/calculate` | *(external API call, no DB)* | TC-SHIP-01 | MUST | 🔲 |
| UC-SHIP-02 | Tạo vận đơn | `AdminOrderDetail` (tạo vận đơn) | `POST /admin/orders/{id}/shipments` | `shipments` | TC-SHIP-02 | MUST | 🔲 |
| UC-SHIP-03 | Tracking | `OrderDetailPage`, `AccountOrdersPage` | `GET /account/orders/{id}/tracking` | `shipments`, `shipment_events` | TC-SHIP-03 | MUST | 🔲 |
| UC-SHIP-04 | Xử lý webhook carrier | *(background handler)* | `POST /webhooks/shipping` | `shipments`, `shipment_events`, `orders` | TC-SHIP-04 | MUST | 🔲 |
| UC-SHIP-05 | Đổi địa chỉ giao | `AdminOrderDetail` | `PUT /admin/orders/{id}/shipping-address` | `orders` | TC-SHIP-05 | SHOULD | 🔲 |

**Coverage SHIPPING (MVP):** 4/5 MUST use cases = **80%** (UC-SHIP-05 là SHOULD)

---

## 9. Module CUSTOMER SERVICE – CSKH & Bảo hành

| UC ID | Use Case | Screen/UI | API Endpoint | DB Table(s) | Test Case ID | Priority | Status |
|---|---|---|---|---|---|---|---|
| UC-CS-01 | Tạo ticket | `AccountReturnPage` | `POST /account/tickets` | `warranty_cases` | TC-CS-01, TC-CS-02 | MUST | 🔲 |
| UC-CS-02 | Xử lý ticket (admin) | `AdminTicketPage` | `GET /admin/tickets`, `PUT /admin/tickets/{id}` | `warranty_cases` | TC-CS-03, TC-CS-04 | MUST | 🔲 |
| UC-CS-03 | Tra cứu bảo hành IMEI | `AdminIMEILookup` | `GET /admin/inventory/imei/{imei}` | `imei_serials`, `order_items` | TC-CS-05 | MUST | 🔲 |
| UC-CS-04 | Cấu hình chính sách đổi trả | `AdminReturnPolicyPage` | `PUT /admin/settings/return-policy` | `settings` | TC-CS-06 | MUST | 🔲 |

**Coverage CS:** 4/4 use cases = **100%**

---

## 10. Module CMS – Nội dung & SEO

| UC ID | Use Case | Screen/UI | API Endpoint | DB Table(s) | Test Case ID | Priority | Status |
|---|---|---|---|---|---|---|---|
| UC-CMS-01 | Quản lý banner | `AdminBannerPage` | `GET/POST /admin/banners`, `PUT/DELETE /admin/banners/{id}` | `banners` | TC-CMS-01 | MUST | 🔲 |
| UC-CMS-02 | Trang tĩnh | `AdminPagesPage` | `GET/POST /admin/pages`, `PUT/DELETE /admin/pages/{id}` | `pages` | TC-CMS-02 | MUST | 🔲 |
| UC-CMS-03 | Blog/tin tức | `AdminBlogPage` | `GET/POST /admin/blog/posts` | `blog_posts` | TC-CMS-03 | LATER | 🔲 |
| UC-CMS-04 | Auto sitemap | *(build/cron job)* | `GET /sitemap.xml` | `products`, `pages` | TC-CMS-04 | MUST | 🔲 |

**Coverage CMS (MVP):** 3/3 MUST use cases = **100%**

---

## 11. Module REPORTING – Báo cáo

| UC ID | Use Case | Screen/UI | API Endpoint | DB Table(s) | Test Case ID | Priority | Status |
|---|---|---|---|---|---|---|---|
| UC-RPT-01 | Báo cáo doanh thu | `AdminRevenueReport` | `GET /admin/reports/revenue` | `orders`, `order_items` | TC-RPT-01 | MUST | 🔲 |
| UC-RPT-02 | Báo cáo đơn hàng | `AdminOrdersReport` | `GET /admin/reports/orders` | `orders` | TC-RPT-02 | MUST | 🔲 |
| UC-RPT-03 | Báo cáo tồn kho | `AdminInventoryReport` | `GET /admin/reports/inventory` | `inventory` | TC-RPT-03 | MUST | 🔲 |
| UC-RPT-04 | Đối soát COD | `AdminCODReport` | `GET /admin/reports/cod-reconciliation` | `orders`, `payments` | TC-RPT-04 | MUST | 🔲 |
| UC-RPT-05 | Đối soát thanh toán online | `AdminPaymentReport` | `GET /admin/reports/payment-reconciliation` | `payments` | TC-RPT-05 | MUST | 🔲 |

**Coverage REPORTING:** 5/5 use cases = **100%**

---

## 12. Coverage Summary

| Module | Use Cases (Total) | MUST (MVP) | Có API | Có DB Schema | Coverage MVP |
|---|---|---|---|---|---|
| AUTH | 5 | 5 | ✅ 5/5 | ✅ | 100% |
| CATALOG | 6 | 6 | ✅ 6/6 | ✅ | 100% |
| PRICING | 5 | 3 | ✅ 3/3 | ✅ | 60%* |
| INVENTORY | 5 | 5 | ✅ 5/5 | ✅ | 100% |
| ORDER | 6 | 6 | ✅ 6/6 | ✅ | 100% |
| PAYMENT | 5 | 5 | ✅ 5/5 | ✅ | 100% |
| SHIPPING | 5 | 4 | ✅ 4/4 | ✅ | 80%* |
| CUSTOMER SERVICE | 4 | 4 | ✅ 4/4 | ✅ | 100% |
| CMS | 4 | 3 | ⚠️ 3/3 (pending) | ⚠️ Thiếu `banners`, `pages` | 75%* |
| REPORTING | 5 | 5 | ✅ 5/5 | ✅ | 100% |
| **TOTAL** | **50** | **46** | **46/46** | **48/50** | **~95%** |

> *PRICING: 2 use cases (flash sale, gift) là LATER – không tính vào MVP coverage  
> *SHIPPING: UC-SHIP-05 là SHOULD – tính vào Phase 1+  
> *CMS: Endpoint `/admin/banners` và `/admin/pages` cần bổ sung vào OpenAPI (xem [Document Consistency Review](document-consistency-review.md) MISMATCH-08); DB schema cần bổ sung (MISMATCH-03)

**Tổng coverage MVP: ~95% (46/46 MUST use cases có đủ API + DB, trừ CMS cần bổ sung)**

---

## 13. Test Case Index

*Danh sách test case ID đầy đủ xem tại [Testing Strategy](../05-implementation/testing-strategy.md)*

| Prefix | Module | Số lượng TC |
|---|---|---|
| TC-AUTH-xx | Auth | 8 |
| TC-CAT-xx | Catalog | 8 |
| TC-PRC-xx | Pricing | 7 |
| TC-INV-xx | Inventory | 7 |
| TC-ORD-xx | Order | 8 |
| TC-PAY-xx | Payment | 6 |
| TC-SHIP-xx | Shipping | 5 |
| TC-CS-xx | Customer Service | 6 |
| TC-CMS-xx | CMS | 4 |
| TC-RPT-xx | Reporting | 5 |
| **Tổng** | | **~64** |

---

*Tài liệu này phải được cập nhật mỗi khi có thay đổi trong FRS, OpenAPI, hoặc DB Design. Xem [Document Consistency Review](document-consistency-review.md) để biết các mismatch cần xử lý.*
