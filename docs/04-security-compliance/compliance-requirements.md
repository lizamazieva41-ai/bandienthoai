# Compliance Requirements – Yêu cầu tuân thủ pháp lý

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  

---

## 1. Khung pháp lý áp dụng

### 1.1 Danh sách văn bản pháp luật

| Văn bản | Nội dung chính | Ảnh hưởng đến hệ thống |
|---|---|---|
| **Nghị định 52/2013/NĐ-CP** | Thương mại điện tử | Thông báo website, quy định hoạt động TMĐT |
| **Nghị định 85/2021/NĐ-CP** | Sửa đổi 52/2013 | Cập nhật quy định TMĐT, quản lý sàn |
| **Nghị định 13/2023/NĐ-CP** | Bảo vệ dữ liệu cá nhân (PDPA VN) | Thu thập, xử lý, lưu trữ dữ liệu cá nhân |
| **Luật 19/2023/QH15** | Bảo vệ quyền lợi người tiêu dùng | Chính sách đổi trả, thông tin sản phẩm, hóa đơn |
| **Thông tư 47/2014/TT-BCT** | Quản lý website TMĐT bán hàng | Quy định nội dung phải có trên website |
| **Nghị định 126/2015/NĐ-CP** | Dịch vụ thanh toán | Nếu tự xử lý thanh toán (không áp dụng nếu dùng cổng) |

---

## 2. Nghị định 52/2013 và 85/2021 – TMĐT

### 2.1 Thủ tục thông báo website

Website TMĐT bán hàng (không phải sàn) phải **thông báo** với Bộ Công Thương qua cổng [online.gov.vn](https://online.gov.vn) trước khi hoạt động.

**Hồ sơ thông báo bao gồm:**
- Tên, địa chỉ, MST của thương nhân
- Tên miền website
- Các hàng hóa/dịch vụ kinh doanh
- Cơ chế giao dịch

**Thời hạn:** Thông báo trước khi website đi vào hoạt động.

**Action item:**
- [ ] Hoàn tất thông báo website TMĐT tại online.gov.vn trước Go-live
- [ ] Hiển thị logo "Đã thông báo BCT" trên website (footer)

### 2.2 Thông tin bắt buộc trên website

Theo Điều 29 Nghị định 52/2013, website phải hiển thị:
- [ ] Tên, địa chỉ trụ sở của thương nhân
- [ ] Số điện thoại liên lạc
- [ ] Mã số thuế (hoặc số đăng ký kinh doanh)
- [ ] Thông tin về hàng hóa/dịch vụ (mô tả, giá cả, điều kiện giao dịch)
- [ ] Chính sách đổi trả, bảo hành
- [ ] Chính sách vận chuyển và giao nhận
- [ ] Chính sách thanh toán
- [ ] Điều khoản giao dịch chung

### 2.3 Quy định về giao dịch

- Phải xác nhận lại đơn hàng trước khi hoàn tất giao dịch (checkout confirmation)
- Khách hàng phải có khả năng sửa/hủy đơn trước khi xác nhận
- Phải gửi xác nhận giao dịch (qua email/SMS)
- Lưu trữ hợp đồng điện tử (đơn hàng) tối thiểu 2 năm

**Implementation checklist:**
- [ ] Trang review order trước khi confirm (step checkout)
- [ ] Nút "Sửa đơn" và "Hủy" trong quá trình đặt hàng
- [ ] Email/SMS xác nhận đơn hàng tự động
- [ ] Lưu trữ đơn hàng trong database ≥ 2 năm

---

## 3. Nghị định 13/2023/NĐ-CP – Bảo vệ dữ liệu cá nhân

### 3.1 Nguyên tắc xử lý dữ liệu

| Nguyên tắc | Yêu cầu |
|---|---|
| **Hợp pháp** | Chỉ thu thập dữ liệu khi có cơ sở pháp lý (đồng ý, hợp đồng, nghĩa vụ pháp lý) |
| **Mục đích** | Chỉ dùng dữ liệu cho mục đích đã thông báo |
| **Tối thiểu** | Chỉ thu thập dữ liệu cần thiết |
| **Chính xác** | Cho phép cập nhật, sửa dữ liệu |
| **Hạn chế lưu trữ** | Xóa khi hết mục đích hoặc khi yêu cầu |
| **Bảo mật** | Biện pháp kỹ thuật và tổ chức phù hợp |

### 3.2 Dữ liệu cá nhân thu thập và cơ sở pháp lý

| Dữ liệu | Mục đích | Cơ sở pháp lý | Thời gian lưu |
|---|---|---|---|
| Số điện thoại, email | Đăng ký, đăng nhập, thông báo | Đồng ý | Đến khi xóa TK |
| Tên, địa chỉ | Giao hàng, hóa đơn | Thực hiện hợp đồng | 5 năm (thuế) |
| Lịch sử đơn hàng | CSKH, đổi trả | Thực hiện hợp đồng | 5 năm |
| Hành vi duyệt web | Cá nhân hóa, analytics | Đồng ý | 2 năm |
| Thông tin thanh toán (token) | Xử lý thanh toán | Thực hiện hợp đồng | 5 năm |

### 3.3 Quyền của chủ thể dữ liệu

Hệ thống phải đảm bảo chủ thể dữ liệu có thể:

| Quyền | Implementation |
|---|---|
| **Quyền biết** | Hiển thị dữ liệu đang lưu trữ trong trang "Tài khoản" |
| **Quyền đồng ý** | Checkbox đồng ý CSBT khi đăng ký; không check mặc định |
| **Quyền truy cập** | API `GET /account/data-export` – xuất toàn bộ dữ liệu |
| **Quyền chỉnh sửa** | Trang "Thông tin cá nhân" cho phép cập nhật |
| **Quyền xóa** | API `DELETE /account` – xóa tài khoản và dữ liệu liên quan |
| **Quyền hạn chế** | Opt-out marketing email/SMS |
| **Quyền phản đối** | Cơ chế khiếu nại qua ticket |

### 3.4 Biện pháp bảo vệ kỹ thuật

- [ ] Mã hóa dữ liệu nhạy cảm tại rest
- [ ] TLS cho mọi kết nối có dữ liệu cá nhân
- [ ] Access control: chỉ nhân viên có nhu cầu mới xem được PII
- [ ] Audit log mọi truy cập vào dữ liệu cá nhân
- [ ] Thông báo vi phạm dữ liệu cho Bộ Công an trong vòng 72 giờ (Điều 23)

### 3.5 Đánh giá tác động bảo vệ dữ liệu (DPIA)

Thực hiện DPIA trước khi:
- Triển khai hệ thống analytics tracking hành vi
- Tích hợp remarketing (Google Ads, Facebook Pixel)
- Chia sẻ dữ liệu với bên thứ ba (đơn vị vận chuyển, marketing)

---

## 4. Luật 19/2023/QH15 – Bảo vệ quyền lợi người tiêu dùng

### 4.1 Yêu cầu thông tin sản phẩm

Trang sản phẩm phải hiển thị:
- [ ] Tên hàng hóa, thương hiệu, xuất xứ (VN/A, LL/A)
- [ ] Giá bán rõ ràng, đơn vị VNĐ
- [ ] Thông số kỹ thuật chính
- [ ] Thời hạn bảo hành
- [ ] Hướng dẫn sử dụng (link đến tài liệu)

### 4.2 Yêu cầu hợp đồng giao kết từ xa

- [ ] Điều khoản bán hàng phải rõ ràng, dễ đọc, không có điều khoản bất công
- [ ] Khách hàng có quyền từ chối giao dịch trước khi xác nhận
- [ ] Hóa đơn/biên nhận phải được cung cấp sau giao dịch
- [ ] Thông tin hoàn tiền phải được nêu rõ

### 4.3 Quyền đổi trả tối thiểu

Theo Luật BVNTD 2023:
- [ ] Được đổi trả trong trường hợp sản phẩm không đúng mô tả
- [ ] Được đổi trả khi sản phẩm bị lỗi từ nhà sản xuất
- [ ] Thời hạn đổi trả phải được công bố rõ ràng
- [ ] Quy trình đổi trả đơn giản, không gây khó dễ

---

## 5. Thông tư 47/2014/TT-BCT – Nội dung website

### 5.1 Các trang bắt buộc phải có

| Trang | Nội dung bắt buộc |
|---|---|
| Giới thiệu / Về chúng tôi | Tên, địa chỉ, MST, số ĐT, email |
| Chính sách bảo mật | Cách thu thập, sử dụng, bảo vệ dữ liệu |
| Chính sách đổi trả | Điều kiện, quy trình, thời hạn |
| Chính sách vận chuyển | Phạm vi, thời gian, phí |
| Chính sách bảo hành | Thời hạn, điều kiện, quy trình |
| Điều khoản sử dụng | Quy tắc sử dụng website |
| Liên hệ | Địa chỉ, số ĐT, email, giờ làm việc |

---

## 6. Compliance Implementation Roadmap

### Giai đoạn 1 – Trước Go-live (bắt buộc)

- [ ] Hoàn tất thông báo website TMĐT tại online.gov.vn
- [ ] Tạo đầy đủ các trang chính sách (bảo mật, đổi trả, bảo hành, vận chuyển)
- [ ] Hiển thị thông tin doanh nghiệp đầy đủ trên website (footer)
- [ ] Implement cookie consent banner (Nghị định 13/2023)
- [ ] Checkbox đồng ý điều khoản khi đăng ký (không checked mặc định)
- [ ] Email/SMS xác nhận đơn hàng
- [ ] Chức năng xóa tài khoản cho khách hàng
- [ ] Audit log cho dữ liệu cá nhân

### Giai đoạn 2 – Trong 3 tháng đầu vận hành

- [ ] DPIA cho tính năng analytics
- [ ] Data retention policy implementation (xóa dữ liệu hết hạn)
- [ ] API xuất dữ liệu cho khách hàng (right to access)
- [ ] Quy trình xử lý yêu cầu liên quan đến quyền chủ thể dữ liệu

---

## 7. Compliance Monitoring

### 7.1 Internal Audit

| Kiểm tra | Tần suất | Người chịu trách nhiệm |
|---|---|---|
| Review chính sách (còn phù hợp với pháp luật mới?) | 6 tháng/lần | Legal + Product |
| Kiểm tra thông báo website BCT còn hiệu lực | Hàng năm | Admin |
| Data retention policy – xóa dữ liệu quá hạn | Hàng tháng | Tech |
| Review access control vào dữ liệu PII | Hàng quý | Security |

### 7.2 Incident Reporting

Theo Nghị định 13/2023, khi xảy ra vi phạm dữ liệu cá nhân:
1. Thông báo nội bộ trong 1 giờ
2. Ngăn chặn và điều tra trong 24 giờ
3. Báo cáo Bộ Công an (Cục An ninh mạng) trong 72 giờ (nếu nghiêm trọng)
4. Thông báo cho chủ thể dữ liệu bị ảnh hưởng

*Xem chi tiết quy trình tại [Security Architecture](security-architecture.md) và [Data Protection](data-protection.md).*
