# Business Case – Ứng dụng Web Bán Điện Thoại

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  

---

## 1. Bối cảnh kinh doanh

### 1.1 Thị trường mục tiêu
Thị trường TMĐT Việt Nam năm 2024 đạt **~32 tỷ USD** (tăng ~27% so với 2023), với doanh số bán lẻ trực tuyến đạt **~22,5 tỷ USD** (tăng ~30%). Điện thoại di động là mặt hàng có giá trị trung bình đơn hàng cao (3–30 triệu VNĐ), đòi hỏi trải nghiệm mua hàng online chuyên nghiệp.

### 1.2 Vấn đề hiện tại
- Chuỗi/cửa hàng bán điện thoại hiện chủ yếu nhận đơn qua điện thoại, Zalo, Facebook – thiếu hệ thống tự động hóa
- Mất kiểm soát tồn kho khi bán đa kênh
- Không có khả năng theo dõi đơn hàng, trạng thái vận chuyển real-time
- Khó mở rộng tệp khách hàng ra ngoài khu vực địa lý hiện tại

### 1.3 Cơ hội
- Người tiêu dùng Việt ngày càng quen mua điện thoại online (theo xu hướng của TGDĐ, FPT Shop, CellphoneS)
- Chi phí tìm kiếm khách hàng qua SEO thấp hơn quảng cáo trả phí về lâu dài
- Trả góp & BNPL mở ra phân khúc khách hàng mới (7–30 triệu VNĐ)

---

## 2. Phân tích giải pháp

### 2.1 Các lựa chọn

| Lựa chọn | Mô tả | Ưu điểm | Nhược điểm |
|---|---|---|---|
| **A. Không làm gì** | Duy trì kênh bán thủ công | Không tốn chi phí đầu tư | Mất thị phần dần, không scale được |
| **B. Dùng SaaS (Shopify/Haravan)** | Thuê nền tảng có sẵn | Nhanh ra mắt, ít rủi ro kỹ thuật | Chi phí thuê lâu dài cao, hạn chế tùy biến nghiệp vụ đặc thù điện thoại (IMEI, bảo hành) |
| **C. Xây dựng tùy chỉnh (khuyến nghị)** | Xây hệ thống riêng theo nghiệp vụ | Toàn quyền kiểm soát, tối ưu cho điện thoại | Đầu tư ban đầu cao hơn, cần team kỹ thuật |

**Lựa chọn khuyến nghị: C – Xây dựng tùy chỉnh** với kiến trúc modular, có thể tích hợp với POS hiện tại.

### 2.2 Lý do chọn giải pháp C
1. **Nghiệp vụ đặc thù điện thoại** (IMEI tracking, đồng kiểm, bảo hành, trade-in) không được hỗ trợ tốt trên các nền tảng SaaS chung
2. **Tích hợp sâu với hệ thống cửa hàng** (KiotViet, Sapo) – cần API tùy chỉnh
3. **Chi phí vận hành dài hạn thấp hơn** so với thuê SaaS khi doanh số tăng
4. **Toàn quyền về dữ liệu** khách hàng và đơn hàng

---

## 3. Phân tích lợi ích & chi phí

### 3.1 Chi phí

#### Chi phí phát triển (một lần)
| Hạng mục | Ước tính |
|---|---|
| Phát triển backend (6 tháng × 3 dev) | 540–900 triệu VNĐ |
| Phát triển frontend (6 tháng × 2 dev) | 360–600 triệu VNĐ |
| DevOps & Infrastructure setup | 60–120 triệu VNĐ |
| QA & Testing | 90–150 triệu VNĐ |
| UI/UX Design | 60–120 triệu VNĐ |
| **Tổng (phương án trung)** | **900 triệu – 1,8 tỷ VNĐ** |

#### Chi phí vận hành hàng tháng
| Hạng mục | Ước tính/tháng |
|---|---|
| Cloud infrastructure (servers, CDN, storage) | 10–25 triệu VNĐ |
| Phí cổng thanh toán (0.5–2% doanh thu) | Biến đổi theo doanh số |
| Phí giao vận (trả qua đối soát) | Biến đổi theo đơn hàng |
| Maintenance & support | 10–20 triệu VNĐ |
| Marketing (SEO, content) | 5–15 triệu VNĐ |
| **Tổng cố định ước tính** | **30–80 triệu VNĐ/tháng** |

### 3.2 Lợi ích dự kiến

#### Lợi ích định lượng
| Lợi ích | Năm 1 | Năm 2 | Năm 3 |
|---|---|---|---|
| Doanh thu kênh online mới (ước tính 5–15% tổng doanh thu) | 1–3 tỷ VNĐ | 2–6 tỷ VNĐ | 4–12 tỷ VNĐ |
| Tiết kiệm chi phí nhân sự xử lý đơn thủ công | 120–240 triệu VNĐ | 120–240 triệu VNĐ | 120–240 triệu VNĐ |
| Giảm tỷ lệ đơn hàng lỗi/tranh chấp do hệ thống tự động | 50–100 triệu VNĐ | 80–150 triệu VNĐ | 100–200 triệu VNĐ |

#### Lợi ích định tính
- Nâng cao thương hiệu và uy tín trong mắt khách hàng
- Thu thập dữ liệu khách hàng để phân tích và remarketing
- Tạo nền tảng mở rộng đa kênh (sàn TMĐT, ứng dụng mobile)
- Khả năng scale theo tăng trưởng kinh doanh

### 3.3 ROI Analysis

```
Đầu tư ban đầu (phương án trung): ~1,35 tỷ VNĐ (trung vị)
Chi phí vận hành năm 1: ~660 triệu VNĐ (55 triệu/tháng × 12)
Tổng chi phí năm 1: ~2,01 tỷ VNĐ

Doanh thu tăng thêm năm 1 (ước tính trung): ~2 tỷ VNĐ
Lợi nhuận gộp kênh online (giả định 15% margin): ~300 triệu VNĐ

→ Break-even dự kiến: 18–24 tháng
→ ROI năm 3: 150–300% (với tăng trưởng 50% mỗi năm)
```

*Lưu ý: Các con số này là ước tính nội bộ tham khảo, phụ thuộc vào chiến lược giá, marketing, và hiệu quả vận hành thực tế.*

---

## 4. Phân tích rủi ro kinh doanh

| Rủi ro | Xác suất | Tác động | Biện pháp |
|---|---|---|---|
| Doanh số online thấp hơn kỳ vọng | Trung bình | Cao | Đầu tư SEO/content từ đầu; theo dõi conversion rate hàng tuần |
| Phí vận chuyển/hoàn hàng cao | Cao | Trung bình | Đàm phán hợp đồng khối lượng với GHN/GHTK |
| Cạnh tranh giá từ sàn TMĐT | Cao | Trung bình | Tập trung vào dịch vụ (bảo hành, đổi trả) làm điểm khác biệt |
| Phát sinh chi phí kỹ thuật ngoài kế hoạch | Trung bình | Trung bình | Dự phòng 20% ngân sách phát triển |

---

## 5. Điều kiện tiên quyết

1. Doanh nghiệp đã có đăng ký kinh doanh, MST, tài khoản ngân hàng doanh nghiệp
2. Hợp đồng với ít nhất 1 cổng thanh toán đã được ký trước khi Go-live
3. Hợp đồng với ít nhất 1 đơn vị vận chuyển đã được ký
4. Team kỹ thuật được tuyển dụng hoặc outsource đầy đủ trước khi bắt đầu Sprint 1

---

## 6. Khuyến nghị

Phê duyệt dự án với **phương án trung bình** (12–16 tuần, 900 triệu – 1,8 tỷ VNĐ) với các điều kiện:
1. Bắt đầu thủ tục đăng ký cổng thanh toán và thông báo website TMĐT ngay khi dự án được phê duyệt
2. Cam kết nguồn lực kỹ thuật ổn định trong suốt 16 tuần đầu
3. Ưu tiên Go-live với MVP trước, không bổ sung yêu cầu mới trong giai đoạn MVP trừ trường hợp critical

*Xem chi tiết lộ trình tại [Roadmap](../05-implementation/roadmap.md) và kế hoạch nguồn lực tại [Resource Plan](../07-project-management/resource-plan.md).*
