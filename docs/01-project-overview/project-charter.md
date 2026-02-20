# Project Charter – Ứng dụng Web Bán Điện Thoại

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  
**Trạng thái:** Đã phê duyệt  

---

## 1. Thông tin dự án

| Hạng mục | Nội dung |
|---|---|
| **Tên dự án** | Xây dựng ứng dụng web thương mại điện tử bán điện thoại di động |
| **Project Sponsor** | Ban lãnh đạo doanh nghiệp |
| **Project Manager** | TBD – xác nhận trước Sprint 0 |
| **Ngày bắt đầu** | 2026-02-20 |
| **Ngày dự kiến Go-live** | 2026-05-30 (MVP) |
| **Ngân sách ước tính** | 900 triệu – 1,8 tỷ VNĐ (phương án trung) |

---

## 2. Mục tiêu dự án

### 2.1 Mục tiêu kinh doanh
1. Xây dựng kênh bán hàng trực tuyến chuyên nghiệp cho chuỗi/cửa hàng bán điện thoại di động tại Việt Nam.
2. Tăng doanh thu thông qua kênh online, mở rộng tệp khách hàng ngoài phạm vi địa lý cửa hàng vật lý.
3. Giảm chi phí vận hành thông qua tự động hoá quy trình đặt hàng, xử lý thanh toán, và quản lý giao vận.
4. Xây dựng niềm tin khách hàng qua chính sách bảo hành/đổi trả minh bạch và hệ thống CSKH chuyên nghiệp.

### 2.2 Mục tiêu kỹ thuật
1. Triển khai hệ thống modular monolith có khả năng mở rộng thành microservices theo nhu cầu tăng trưởng.
2. Đạt Core Web Vitals ở mức "Good" (LCP < 2.5s, INP < 200ms, CLS < 0.1).
3. Hỗ trợ mobile-first, SEO-friendly với schema markup theo chuẩn Google.
4. Tích hợp đầy đủ các cổng thanh toán (VNPAY, MoMo, ZaloPay) và đơn vị vận chuyển (GHN, GHTK).
5. Tuân thủ các quy định pháp lý về TMĐT và bảo vệ dữ liệu cá nhân tại Việt Nam.

---

## 3. Phạm vi dự án

### 3.1 Trong phạm vi (In-scope)

**MVP (Giai đoạn 1 – 12–16 tuần):**
- Module Catalog: Quản lý sản phẩm, biến thể (màu/dung lượng), SKU, giá bán
- Module Inventory: Tồn kho theo kho/chi nhánh, đặt giữ hàng khi tạo đơn
- Module Order: Tạo đơn, quản lý trạng thái đơn hàng đầy đủ
- Module Payment: Tích hợp VNPAY, MoMo, ZaloPay; xử lý hoàn tiền
- Module Shipping: Tích hợp GHN, GHTK; tracking vận đơn; webhook
- Module Auth: Đăng ký/đăng nhập khách hàng; phân quyền admin
- Module CMS: Quản lý nội dung, banner, trang tĩnh, SEO metadata
- Module Customer Service: Ticket hỗ trợ cơ bản, chính sách đổi trả
- Module Reporting: Báo cáo doanh thu, tồn kho, đơn hàng cơ bản
- Storefront mobile-first, SSR/SSG để tối ưu SEO

**Giai đoạn 2 (Mở rộng vận hành):**
- Khuyến mãi nâng cao: voucher engine, flash sale, combo
- Báo cáo đối soát COD/hoàn tiền
- Tích hợp POS/đa kênh (KiotViet, Sapo, Haravan)

**Giai đoạn 3 (Tăng trưởng):**
- Module Trade-in: Định giá máy cũ, bù trừ đơn hàng
- Loyalty/CRM: Điểm thưởng, hạng thành viên, remarketing
- SEO nâng cao: Core Web Vitals, structured data

### 3.2 Ngoài phạm vi (Out-of-scope)
- Xây dựng ứng dụng mobile native (iOS/Android) – giai đoạn sau
- Tự xây dựng cổng thanh toán
- Tích hợp ERP phức tạp (SAP, Oracle)
- Hệ thống quản lý kho thực thể (WMS) nâng cao
- Marketplace (nhiều người bán) – đây là website 1 seller

---

## 4. Stakeholders

| Stakeholder | Vai trò | Mức độ tham gia |
|---|---|---|
| Ban lãnh đạo | Project Sponsor, phê duyệt ngân sách | Tham vấn định kỳ |
| Trưởng phòng kinh doanh | Chủ sở hữu yêu cầu nghiệp vụ | Tham gia thường xuyên |
| Trưởng phòng kho vận | Yêu cầu nghiệp vụ tồn kho/giao vận | Tham gia thường xuyên |
| Kế toán trưởng | Yêu cầu đối soát, hóa đơn | Tham gia khi cần |
| Project Manager | Điều phối dự án, quản lý timeline | Toàn thời gian |
| Tech Lead | Kiến trúc kỹ thuật, code review | Toàn thời gian |
| Backend Developers (2–3) | Phát triển core modules | Toàn thời gian |
| Frontend Developer (1–2) | Storefront & Admin UI | Toàn thời gian |
| QA Engineer (1) | Kiểm thử, UAT | Từ sprint 3 |
| DevOps Engineer (1) | Infrastructure, CI/CD, monitoring | Từ sprint 2 |
| Khách hàng cuối | Người dùng mua hàng | UAT |
| VNPAY / MoMo / ZaloPay | Đối tác thanh toán | Tích hợp |
| GHN / GHTK | Đối tác giao vận | Tích hợp |

---

## 5. Success Criteria (Tiêu chí thành công)

### 5.1 Tiêu chí kỹ thuật
- [ ] Hệ thống vượt qua 100% test cases trong phạm vi MVP
- [ ] Core Web Vitals đạt "Good" trên Lighthouse (desktop và mobile)
- [ ] Uptime ≥ 99.5% trong tháng đầu vận hành
- [ ] Thời gian phản hồi API < 200ms ở percentile 95 (P95) với tải thông thường
- [ ] Zero critical security vulnerability trước Go-live (theo OWASP Top 10)
- [ ] Tích hợp thành công ≥ 2 cổng thanh toán và ≥ 1 đơn vị vận chuyển

### 5.2 Tiêu chí nghiệp vụ
- [ ] Xử lý thành công đơn hàng end-to-end trong môi trường UAT
- [ ] CSKH có thể xử lý ticket đổi trả trong hệ thống
- [ ] Admin có thể quản lý sản phẩm, tồn kho, đơn hàng mà không cần hỗ trợ kỹ thuật
- [ ] Báo cáo doanh thu và tồn kho khớp với dữ liệu thực tế

### 5.3 Tiêu chí tuân thủ
- [ ] Hoàn tất thủ tục thông báo website TMĐT bán hàng tại online.gov.vn
- [ ] Chính sách bảo mật, đổi trả, bảo hành được công bố công khai
- [ ] Hệ thống không lưu trữ dữ liệu thẻ ngân hàng (PCI DSS tokenization)

---

## 6. Ràng buộc & Giả định

### 6.1 Ràng buộc
- **Ngân sách:** Không vượt quá 1,8 tỷ VNĐ cho giai đoạn MVP
- **Thời gian:** Go-live MVP trong vòng 16 tuần từ ngày khởi động
- **Công nghệ:** Sử dụng các thư viện/framework mã nguồn mở phổ biến
- **Pháp lý:** Phải tuân thủ Nghị định 52/2013, 85/2021, 13/2023 và Luật 19/2023/QH15 trước khi ra mắt

### 6.2 Giả định
- Doanh nghiệp đã có giấy phép kinh doanh hợp lệ và đăng ký thuế
- Nguồn hàng (sản phẩm, nhà cung cấp) đã được xác định
- Team phát triển có kinh nghiệm với các cổng thanh toán và đơn vị vận chuyển tại VN
- Cổng thanh toán (VNPAY/MoMo/ZaloPay) đồng ý ký hợp đồng với doanh nghiệp

---

## 7. Rủi ro chính

| Rủi ro | Xác suất | Mức độ ảnh hưởng | Biện pháp giảm thiểu |
|---|---|---|---|
| Trễ tích hợp cổng thanh toán | Trung bình | Cao | Bắt đầu đăng ký cổng thanh toán từ tuần 1 song song với phát triển |
| Yêu cầu thay đổi giữa chừng | Cao | Trung bình | Áp dụng quy trình Change Management nghiêm ngặt |
| Thiếu nhân lực kỹ thuật | Trung bình | Cao | Có kế hoạch dự phòng nhân sự freelance |
| Lỗ hổng bảo mật | Thấp | Rất cao | Thực hiện security review trước Go-live |

*Xem chi tiết tại [Risk Register](../07-project-management/risk-register.md).*

---

## 8. Phê duyệt

| Vai trò | Họ tên | Ngày ký | Chữ ký |
|---|---|---|---|
| Project Sponsor | | | |
| Project Manager | | | |
| Tech Lead | | | |
