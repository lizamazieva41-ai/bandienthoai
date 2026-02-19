# Business Requirements Document (BRD)

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  
**Chuẩn tham chiếu:** ISO/IEC/IEEE 29148:2018  

---

## 1. Giới thiệu

### 1.1 Mục đích
Tài liệu này mô tả các yêu cầu nghiệp vụ của ứng dụng web thương mại điện tử chuyên bán điện thoại di động, làm cơ sở cho việc xây dựng Functional Requirements Specification (FRS) và thiết kế kỹ thuật.

### 1.2 Phạm vi
Toàn bộ hệ thống bao gồm: storefront dành cho khách hàng, backoffice dành cho admin/nhân viên, và các tích hợp với bên thứ ba (cổng thanh toán, đơn vị vận chuyển, POS).

### 1.3 Định nghĩa
- **SKU:** Stock Keeping Unit – mã định danh sản phẩm theo biến thể
- **IMEI:** International Mobile Equipment Identity – số nhận dạng thiết bị di động
- **COD:** Cash on Delivery – thu tiền khi giao hàng
- **BNPL:** Buy Now Pay Later – mua trước trả sau
- **Trade-in:** Thu cũ đổi mới
- **Đồng kiểm:** Khách hàng và shipper cùng kiểm tra hàng khi giao nhận

---

## 2. Mô tả kinh doanh

### 2.1 Mô hình kinh doanh
- Bán lẻ điện thoại di động trực tuyến (B2C), với tiềm năng mở rộng sang B2B (doanh nghiệp nhỏ)
- Kết hợp online và offline: website đồng bộ tồn kho với cửa hàng vật lý
- Tự vận hành logistics thông qua đối tác GHN/GHTK, không qua sàn TMĐT

### 2.2 Phân tích đối thủ cạnh tranh
Qua khảo sát 13 website bán điện thoại (TGDĐ, FPT Shop, CellphoneS, Hoàng Hà Mobile, Di Động Việt, Viettel Store, ShopDunk, Tiki, Lazada, Shopee, Apple Store, Samsung Online Store, Amazon), các điểm cạnh tranh cốt lõi là:

1. **Niềm tin** – đồng kiểm, chính hãng, bảo hành rõ ràng
2. **Trả góp & BNPL** – tăng chuyển đổi phân khúc 7–30 triệu VNĐ
3. **Logistics nhanh & tracking** – SLA giao hàng rõ ràng
4. **Mobile-first + SEO** – phần lớn người dùng mua qua điện thoại

---

## 3. Yêu cầu nghiệp vụ chi tiết

### BR-01: Quản lý sản phẩm & giá

**BR-01.1** Hệ thống phải cho phép quản lý sản phẩm điện thoại với cấu trúc phân cấp: Thương hiệu → Model → Biến thể (màu sắc, dung lượng bộ nhớ).

**BR-01.2** Mỗi biến thể sản phẩm phải có mã SKU duy nhất, giá niêm yết, và tùy chọn quản lý theo IMEI/serial number.

**BR-01.3** Hệ thống phải hỗ trợ nhiều mức giá: giá niêm yết, giá sale, giá online (có thể khác giá cửa hàng), giá sỉ (giai đoạn mở rộng).

**BR-01.4** Admin phải có thể thêm/sửa/xóa sản phẩm, quản lý hình ảnh, thông số kỹ thuật, và nội dung SEO (title, description, slug).

**Ưu tiên:** MVP  
**Nguồn:** Khảo sát đối thủ, nhu cầu vận hành cửa hàng

---

### BR-02: Quản lý tồn kho

**BR-02.1** Hệ thống phải theo dõi tồn kho theo từng kho/chi nhánh, với số lượng: tồn thực (on_hand), đã đặt trước (reserved), có thể bán (available = on_hand - reserved).

**BR-02.2** Khi khách tạo đơn, hệ thống phải tự động "giữ" (reserve) số lượng tương ứng để tránh bán quá.

**BR-02.3** Hệ thống phải cảnh báo (email/notification) khi tồn kho một biến thể xuống dưới ngưỡng cảnh báo do admin cài đặt.

**BR-02.4** Hỗ trợ nhập hàng theo lô (purchase order) từ nhà cung cấp, tự động cập nhật tồn kho khi nhập.

**BR-02.5** (Giai đoạn 2) Hỗ trợ chuyển kho giữa các chi nhánh.

**Ưu tiên:** MVP (BR-02.1 đến BR-02.4)  

---

### BR-03: Quản lý đơn hàng

**BR-03.1** Hệ thống phải hỗ trợ vòng đời đơn hàng đầy đủ với các trạng thái:
`Mới → Xác nhận → Đóng gói → Giao vận chuyển → Đang giao → Đã giao → Hoàn tất | Huỷ | Hoàn trả`

**BR-03.2** Admin phải có thể tạo đơn thủ công (cho khách gọi điện), sửa địa chỉ, ghi chú nội bộ, và in phiếu giao hàng.

**BR-03.3** Khách hàng phải có thể theo dõi trạng thái đơn hàng và vận chuyển theo thời gian thực.

**BR-03.4** Hệ thống phải hỗ trợ hủy đơn (trước khi giao vận chuyển) và hoàn trả hàng (với lý do và bằng chứng ảnh/video).

**BR-03.5** Mỗi đơn hàng phải có log lịch sử trạng thái với timestamp và người thực hiện.

**Ưu tiên:** MVP  

---

### BR-04: Thanh toán

**BR-04.1** Hệ thống phải hỗ trợ các phương thức thanh toán: COD, chuyển khoản ngân hàng, thẻ nội địa/quốc tế qua VNPAY, ví MoMo, ZaloPay, và BNPL qua Kredivo (giai đoạn sau).

**BR-04.2** Khi thanh toán thành công, hệ thống phải cập nhật trạng thái đơn hàng tự động qua webhook.

**BR-04.3** Hệ thống phải hỗ trợ hoàn tiền (refund) một phần hoặc toàn phần, với log đối soát.

**BR-04.4** Hệ thống **không được lưu trữ** thông tin thẻ ngân hàng (CVV, số thẻ đầy đủ) – chỉ lưu token từ cổng thanh toán.

**BR-04.5** Admin phải có báo cáo đối soát thanh toán hàng ngày/tuần/tháng.

**Ưu tiên:** MVP  

---

### BR-05: Giao vận

**BR-05.1** Hệ thống phải tự động tính phí ship dựa trên địa chỉ giao hàng và trọng lượng/kích thước kiện hàng thông qua API của GHN/GHTK.

**BR-05.2** Hệ thống phải tạo vận đơn tự động và in nhãn vận chuyển.

**BR-05.3** Tracking vận chuyển phải được cập nhật real-time qua webhook từ đơn vị vận chuyển.

**BR-05.4** Hệ thống phải hỗ trợ xử lý đơn hoàn (return shipment) khi khách không nhận hàng.

**BR-05.5** Admin phải có báo cáo đối soát COD hàng ngày/tuần.

**Ưu tiên:** MVP  

---

### BR-06: Khuyến mãi

**BR-06.1** Hệ thống phải hỗ trợ các loại voucher: giảm theo % và giảm số tiền cố định, có giới hạn số lần dùng.

**BR-06.2** Hỗ trợ giảm giá trực tiếp trên sản phẩm/model theo thời gian (sale price).

**BR-06.3** (Giai đoạn 2) Hỗ trợ flash sale theo khung giờ, quà tặng kèm, combo phụ kiện, và rule engine khuyến mãi.

**Ưu tiên:** MVP (BR-06.1, BR-06.2), Giai đoạn 2 (BR-06.3)  

---

### BR-07: CSKH & Bảo hành

**BR-07.1** Hệ thống phải có hệ thống ticket CSKH để khách hàng gửi yêu cầu đổi trả, bảo hành, khiếu nại.

**BR-07.2** Mỗi đơn hàng phải có thông tin bảo hành (ngày bắt đầu, thời hạn, điều kiện) theo quy định từng sản phẩm.

**BR-07.3** Hệ thống phải lưu bằng chứng (ảnh/video) khi xử lý đổi trả/bảo hành.

**BR-07.4** (Giai đoạn sau) Tra cứu lịch sử bảo hành theo IMEI.

**Ưu tiên:** MVP (BR-07.1 đến BR-07.3)  

---

### BR-08: Nội dung & SEO

**BR-08.1** Admin phải quản lý được banner trang chủ, trang danh mục, landing page sản phẩm.

**BR-08.2** Mỗi trang sản phẩm phải có SEO metadata đầy đủ (title, meta description, canonical URL, structured data schema.org).

**BR-08.3** Hệ thống phải tự động tạo sitemap.xml và robots.txt theo chuẩn Google Search Central.

**BR-08.4** Storefront phải hỗ trợ SSR (Server-Side Rendering) hoặc SSG (Static Site Generation) để tối ưu crawl và tốc độ tải.

**Ưu tiên:** MVP  

---

### BR-09: Báo cáo & Phân tích

**BR-09.1** Dashboard admin phải hiển thị tóm tắt: doanh thu hôm nay, số đơn mới, tồn kho thấp, đơn đang vận chuyển.

**BR-09.2** Báo cáo doanh thu phải lọc được theo khoảng thời gian, sản phẩm, nhân viên tạo đơn.

**BR-09.3** Báo cáo tồn kho phải hiển thị: tồn theo kho, sản phẩm sắp hết, sản phẩm chậm bán.

**BR-09.4** (Giai đoạn 2) Báo cáo lợi nhuận gộp, tỷ lệ hủy/hoàn, hiệu quả khuyến mãi.

**Ưu tiên:** MVP (BR-09.1 đến BR-09.3)  

---

### BR-10: Trade-in (Giai đoạn 3)

**BR-10.1** Hệ thống phải cho phép khách hàng nhập thông tin máy cũ (brand, model, tình trạng) để nhận định giá thu cũ.

**BR-10.2** Giá thu cũ phải được trừ trực tiếp vào đơn hàng mới tại bước checkout.

**BR-10.3** Admin phải có quy trình kiểm định (audit) máy cũ khi nhận tại cửa hàng/giao vận.

**Ưu tiên:** Giai đoạn 3  

---

## 4. Yêu cầu phi chức năng tổng quát

*(Chi tiết tại [Non-Functional Requirements](non-functional-requirements.md))*

- **Hiệu năng:** Core Web Vitals "Good", thời gian phản hồi < 200ms P95
- **Bảo mật:** Tuân thủ OWASP Top 10, không lưu dữ liệu thẻ
- **Khả dụng:** Uptime ≥ 99.5%
- **Tuân thủ:** Nghị định 52/2013, 85/2021, 13/2023; Luật 19/2023/QH15
- **Mobile-first:** Tối ưu trải nghiệm trên thiết bị di động

---

## 5. Ma trận truy xuất yêu cầu (Requirements Traceability)

| BR ID | Mô tả | Module kỹ thuật | Ưu tiên |
|---|---|---|---|
| BR-01 | Quản lý sản phẩm & giá | Catalog | MVP |
| BR-02 | Tồn kho | Inventory | MVP |
| BR-03 | Đơn hàng | Order | MVP |
| BR-04 | Thanh toán | Payment | MVP |
| BR-05 | Giao vận | Shipping | MVP |
| BR-06.1-2 | Voucher & sale price | Pricing & Promotion | MVP |
| BR-06.3 | Flash sale, combo, rule engine | Pricing & Promotion | Giai đoạn 2 |
| BR-07 | CSKH & Bảo hành | Customer Service | MVP |
| BR-08 | Nội dung & SEO | CMS | MVP |
| BR-09.1-3 | Báo cáo cơ bản | Reporting | MVP |
| BR-09.4 | Báo cáo nâng cao | Reporting | Giai đoạn 2 |
| BR-10 | Trade-in | Trade-in Module | Giai đoạn 3 |
