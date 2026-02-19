# User Stories – Ứng dụng Web Bán Điện Thoại

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  
**Format:** As a [persona], I want to [action] so that [benefit]  

---

## Persona Overview

| Persona | Mô tả | Mục tiêu chính |
|---|---|---|
| **P1 – Người mua giá tốt** | Săn deal, so sánh giá, dùng voucher/trả góp | Mua được điện thoại với giá tốt nhất |
| **P2 – Người mua chính hãng** | Ưu tiên bảo hành, chính hãng, đáng tin | Yên tâm về chất lượng và chính sách hậu mãi |
| **P3 – Người mua cần gấp** | Cần nhận hàng nhanh, giao nhanh/nhận tại cửa hàng | Nhận hàng trong ngày hoặc sớm nhất |
| **P4 – Người đổi máy** | Trade-in, bù tiền, nâng cấp | Tối ưu giá trị máy cũ khi nâng cấp |
| **P5 – Khách doanh nghiệp** | Mua số lượng, xuất hóa đơn, giá sỉ | Mua nhanh, giá tốt, hóa đơn đầy đủ |

---

## Epic 1: Khám phá & Tìm kiếm sản phẩm

### US-001 (P1, P2, P3)
**As a** người mua hàng,  
**I want to** tìm kiếm điện thoại theo tên/model/thương hiệu,  
**So that** tôi có thể nhanh chóng tìm thấy sản phẩm mình cần.

**Acceptance Criteria:**
- [ ] Kết quả tìm kiếm xuất hiện trong < 500ms
- [ ] Hỗ trợ tìm kiếm không dấu (iphone = iPhone)
- [ ] Hiển thị gợi ý autocomplete sau 2 ký tự
- [ ] Không có kết quả thì hiển thị sản phẩm thay thế

**Story Points:** 5

---

### US-002 (P1, P2)
**As a** người mua hàng,  
**I want to** lọc sản phẩm theo thương hiệu, khoảng giá, dung lượng, và màu sắc,  
**So that** tôi có thể thu hẹp lựa chọn theo ngân sách và nhu cầu.

**Acceptance Criteria:**
- [ ] Filter tổ hợp: brand + price range + storage + color
- [ ] URL cập nhật khi apply filter (shareable)
- [ ] Xóa từng filter hoặc xóa tất cả
- [ ] Hiển thị số lượng kết quả sau khi lọc

**Story Points:** 8

---

### US-003 (P2)
**As a** người mua chính hãng,  
**I want to** xem thông tin chi tiết: bảo hành, nguồn gốc (Việt Nam/A), và chính sách đổi trả,  
**So that** tôi yên tâm trước khi quyết định mua.

**Acceptance Criteria:**
- [ ] Trang sản phẩm hiển thị rõ: "Bảo hành X tháng chính hãng"
- [ ] Hiển thị chính sách đổi trả trong vòng N ngày
- [ ] Badge "Chính hãng" / "Nguyên seal" hiển thị nổi bật
- [ ] Link đến trang chính sách bảo hành đầy đủ

**Story Points:** 3

---

### US-004 (P1)
**As a** người mua giá tốt,  
**I want to** xem flash sale và sản phẩm đang giảm giá,  
**So that** tôi không bỏ lỡ ưu đãi tốt.

**Acceptance Criteria:**
- [ ] Trang chủ hiển thị countdown timer cho flash sale
- [ ] Badge "Giảm X%" / "Tiết kiệm X triệu" trên ảnh sản phẩm
- [ ] Flash sale tự động kết thúc đúng giờ đã cài đặt

**Story Points:** 5

---

## Epic 2: Quy trình mua hàng (Checkout)

### US-010 (P1, P2, P3)
**As a** khách hàng,  
**I want to** thêm sản phẩm vào giỏ hàng và điều chỉnh số lượng,  
**So that** tôi có thể chuẩn bị đơn hàng trước khi thanh toán.

**Acceptance Criteria:**
- [ ] Thêm vào giỏ không cần đăng nhập (guest cart)
- [ ] Giỏ hàng lưu tối thiểu 7 ngày (localStorage)
- [ ] Hiển thị cảnh báo nếu sản phẩm hết hàng khi xem giỏ
- [ ] Hiển thị tổng tiền cập nhật realtime khi thay đổi số lượng

**Story Points:** 5

---

### US-011 (P1)
**As a** người mua giá tốt,  
**I want to** nhập mã voucher tại bước thanh toán,  
**So that** tôi được giảm giá theo ưu đãi.

**Acceptance Criteria:**
- [ ] Nhập mã và xem số tiền được giảm trước khi confirm
- [ ] Thông báo lỗi rõ ràng: mã không tồn tại / đã hết hạn / không đủ điều kiện
- [ ] Chỉ áp dụng 1 voucher mỗi đơn

**Story Points:** 5

---

### US-012 (P1, P4)
**As a** người mua,  
**I want to** thanh toán trả góp hoặc BNPL,  
**So that** tôi có thể mua điện thoại cao cấp mà không cần trả toàn bộ tiền ngay.

**Acceptance Criteria:**
- [ ] Hiển thị các tùy chọn trả góp tại trang checkout (số tháng, lãi suất)
- [ ] Điều hướng đến cổng BNPL (Kredivo) để hoàn tất
- [ ] Đơn hàng chỉ xác nhận sau khi nhận callback thành công từ BNPL

**Story Points:** 8

---

### US-013 (P2)
**As a** người mua chính hãng,  
**I want to** chọn phương thức "đồng kiểm khi nhận hàng",  
**So that** tôi có thể kiểm tra sản phẩm trước khi ký nhận.

**Acceptance Criteria:**
- [ ] Tùy chọn "Cho phép đồng kiểm" tại bước chọn vận chuyển
- [ ] Ghi chú đặc biệt truyền sang carrier khi tạo vận đơn
- [ ] Nếu có vấn đề khi đồng kiểm: shipper chờ hoặc mang hàng về

**Story Points:** 5

---

### US-014 (P3)
**As a** người cần hàng gấp,  
**I want to** chọn giao hàng nhanh trong ngày hoặc nhận tại cửa hàng,  
**So that** tôi có thể nhận hàng sớm nhất có thể.

**Acceptance Criteria:**
- [ ] Hiển thị "Giao trong ngày" nếu đặt trước 12h (cho địa chỉ trong phạm vi)
- [ ] Tùy chọn "Nhận tại cửa hàng" với danh sách cửa hàng có tồn
- [ ] Thời gian giao ước tính cập nhật theo địa chỉ nhập

**Story Points:** 8

---

## Epic 3: Tài khoản & Theo dõi đơn hàng

### US-020 (P1, P2, P3)
**As a** khách hàng đã đăng nhập,  
**I want to** xem lịch sử đơn hàng và trạng thái giao hàng,  
**So that** tôi biết đơn hàng của mình đang ở đâu.

**Acceptance Criteria:**
- [ ] Trang "Đơn hàng của tôi" hiển thị đầy đủ danh sách đơn
- [ ] Mỗi đơn có link theo dõi vận chuyển (tracking)
- [ ] Nhận thông báo email/SMS khi đơn thay đổi trạng thái quan trọng

**Story Points:** 5

---

### US-021 (P2, P3)
**As a** khách hàng,  
**I want to** yêu cầu đổi trả hoặc bảo hành trực tiếp trong tài khoản,  
**So that** tôi không phải đến cửa hàng chỉ để nộp yêu cầu.

**Acceptance Criteria:**
- [ ] Nút "Yêu cầu đổi trả/bảo hành" trên đơn hàng trong cửa sổ cho phép
- [ ] Upload ảnh/video bằng chứng (tối đa 5 file, mỗi file < 20MB)
- [ ] Nhận email xác nhận ticket và mã ticket để theo dõi
- [ ] Hiển thị trạng thái xử lý ticket

**Story Points:** 8

---

### US-022 (P4)
**As a** người muốn đổi máy,  
**I want to** nhập thông tin máy cũ và nhận báo giá thu cũ online,  
**So that** tôi biết được bù thêm bao nhiêu khi mua máy mới.

**Acceptance Criteria:**
- [ ] Form nhập: brand, model, tình trạng (tốt/trầy/vỡ), kèm theo ảnh
- [ ] Hệ thống trả về giá ước tính thu cũ ngay lập tức
- [ ] Có thể áp giá trade-in vào checkout (bù trừ trực tiếp)
- [ ] Nhân viên xác nhận giá cuối khi kiểm định thực tế

**Story Points:** 13

---

## Epic 4: Quản trị (Admin)

### US-030 (Admin)
**As a** admin,  
**I want to** thêm/sửa/xóa sản phẩm với đầy đủ thông tin,  
**So that** danh mục sản phẩm luôn chính xác và đầy đủ cho khách hàng.

**Acceptance Criteria:**
- [ ] Form sản phẩm có: tên, thương hiệu, mô tả (rich text), specs, danh mục
- [ ] Quản lý variants: màu, dung lượng, SKU, giá, ảnh riêng cho mỗi variant
- [ ] Kéo thả để sắp xếp thứ tự ảnh
- [ ] Preview trước khi publish

**Story Points:** 13

---

### US-031 (Admin)
**As a** admin,  
**I want to** xem dashboard tổng quan với doanh thu, đơn mới, và tồn kho thấp,  
**So that** tôi nắm bắt tình hình kinh doanh theo thời gian thực.

**Acceptance Criteria:**
- [ ] Dashboard cập nhật mỗi 5 phút (hoặc refresh thủ công)
- [ ] Hiển thị: doanh thu hôm nay, số đơn theo trạng thái, top sản phẩm, tồn thấp
- [ ] Click vào số liệu để xem chi tiết

**Story Points:** 8

---

### US-032 (Admin)
**As a** nhân viên kho,  
**I want to** nhận hàng từ nhà cung cấp và cập nhật tồn kho,  
**So that** số liệu tồn kho chính xác với thực tế.

**Acceptance Criteria:**
- [ ] Tạo Purchase Order với nhà cung cấp, sản phẩm, số lượng, giá nhập
- [ ] Nhận hàng theo lô: tick từng dòng, ghi chú sai lệch
- [ ] Tồn kho cập nhật tự động sau khi xác nhận nhận hàng
- [ ] In phiếu nhập kho

**Story Points:** 8

---

## Epic 5: Khách doanh nghiệp (Giai đoạn mở rộng)

### US-040 (P5)
**As a** khách doanh nghiệp,  
**I want to** mua nhiều điện thoại cùng lúc và xuất hóa đơn VAT,  
**So that** công ty có thể hoàn thành thủ tục kế toán.

**Acceptance Criteria:**
- [ ] Checkout hỗ trợ nhập thông tin xuất hóa đơn (MST, tên công ty, địa chỉ)
- [ ] Hóa đơn VAT được gửi qua email sau khi đơn Hoàn tất
- [ ] Giá sỉ áp dụng khi đơn hàng đủ điều kiện số lượng

**Story Points:** 8  
**Giai đoạn:** 2–3

---

## Backlog Priority Matrix

| Story | Persona | Impact | Effort | Priority |
|---|---|---|---|---|
| US-001 Tìm kiếm | P1,P2,P3 | High | Medium | P0 |
| US-010 Giỏ hàng | All | High | Low | P0 |
| US-020 Theo dõi đơn | All | High | Low | P0 |
| US-030 Quản lý SP | Admin | High | High | P0 |
| US-031 Dashboard | Admin | High | Medium | P0 |
| US-002 Filter | P1,P2 | High | Medium | P1 |
| US-011 Voucher | P1 | High | Medium | P1 |
| US-013 Đồng kiểm | P2 | Medium | Medium | P1 |
| US-014 Giao nhanh | P3 | High | High | P1 |
| US-021 Đổi trả | P2 | High | Medium | P1 |
| US-012 Trả góp | P1,P4 | High | High | P2 |
| US-022 Trade-in | P4 | Medium | Very High | P3 |
| US-040 B2B | P5 | Medium | High | P3 |
