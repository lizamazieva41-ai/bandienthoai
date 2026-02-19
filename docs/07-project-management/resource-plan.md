# Resource Plan – Kế hoạch Nguồn lực & Ngân sách

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  
**Chuẩn tham chiếu:** PMBOK 7th Edition  

---

## 1. Team Structure & Resource Allocation

### 1.1 Cơ cấu nhân lực theo giai đoạn

| Vai trò | Giai đoạn 1 (MVP) | Giai đoạn 2 | Giai đoạn 3 | Loại |
|---|---|---|---|---|
| Project Manager | 1 FTE | 1 FTE | 0.5 FTE | Fulltime |
| Tech Lead / Architect | 1 FTE | 1 FTE | 1 FTE | Fulltime |
| Backend Developer | 2 FTE | 2 FTE | 2–3 FTE | Fulltime |
| Frontend Developer | 1–2 FTE | 1 FTE | 1–2 FTE | Fulltime |
| DevOps Engineer | 1 FTE | 1 FTE | 1 FTE | Fulltime |
| QA Engineer | 1 FTE | 1 FTE | 1 FTE | Fulltime |
| UI/UX Designer | 1 FTE | 0.5 FTE | 0.5 FTE | Part-time |
| **Tổng** | **8–9 FTE** | **7–8 FTE** | **7–10 FTE** | |

### 1.2 Responsibility Matrix (MVP)

| Deliverable | PM | Tech Lead | Backend | Frontend | DevOps | QA | Designer |
|---|---|---|---|---|---|---|---|
| Project plan & tracking | A | C | I | I | I | I | I |
| DB schema design | I | A | R | I | I | C | I |
| API design | I | A | R | C | I | C | I |
| Backend modules | I | A | R | I | I | C | I |
| Frontend storefront | I | C | C | A,R | I | C | C |
| Admin backoffice | I | C | C | A,R | I | C | C |
| CI/CD pipeline | C | C | I | I | A,R | I | I |
| Infrastructure setup | I | C | I | I | A,R | I | I |
| Test plan & execution | C | C | C | C | I | A,R | I |
| UI/UX design | C | I | I | C | I | I | A,R |
| Go-live decision | A | C | C | C | C | C | I |

---

## 2. Chi phí phát triển

### 2.1 Nhân sự (Phương án trung – 16 tuần)

*Giả định mức lương thị trường tại TP.HCM, bao gồm BHXH và overhead:*

| Vai trò | Mức lương/tháng | Số tháng | Chi phí |
|---|---|---|---|
| Project Manager | 35–50 triệu VNĐ | 4 | 140–200 triệu |
| Tech Lead | 60–90 triệu VNĐ | 4 | 240–360 triệu |
| Backend Developer × 2 | 40–60 triệu VNĐ/người | 4 | 320–480 triệu |
| Frontend Developer × 1–2 | 35–55 triệu VNĐ/người | 4 | 140–440 triệu |
| DevOps Engineer | 45–65 triệu VNĐ | 4 | 180–260 triệu |
| QA Engineer | 25–40 triệu VNĐ | 3 | 75–120 triệu |
| UI/UX Designer | 30–45 triệu VNĐ | 2 | 60–90 triệu |
| **Tổng nhân sự** | | | **1,155–1,950 triệu VNĐ** |

### 2.2 Chi phí khác trong phát triển

| Hạng mục | Ước tính |
|---|---|
| Development tools & licenses | 10–20 triệu VNĐ |
| Design tools (Figma, etc.) | 3–5 triệu VNĐ |
| Testing tools (Playwright Cloud) | 5–10 triệu VNĐ |
| Cloud costs (dev/staging) | 10–20 triệu VNĐ (4 tháng) |
| Security testing tools | 5–15 triệu VNĐ |
| Contingency (20% nhân sự) | 150–390 triệu VNĐ |

### 2.3 Tóm tắt Chi phí Phát triển

| Phương án | Chi phí nhân sự | Chi phí khác | **Tổng** |
|---|---|---|---|
| Thấp (MVP tinh gọn, 10 tuần, 4–6 người) | 350–600 triệu | 50–100 triệu | **400–700 triệu** |
| **Trung (khuyến nghị, 16 tuần, 6–8 người)** | **800–1,200 triệu** | **100–200 triệu** | **900 triệu–1,4 tỷ** |
| Cao (6–9 tháng, 10–14 người) | 2–4 tỷ | 300–500 triệu | **2,3–4,5 tỷ** |

---

## 3. Chi phí Vận hành Hàng tháng

### 3.1 Infrastructure Costs

| Hạng mục | Cấu hình | Chi phí/tháng |
|---|---|---|
| App Server (2 instances) | 4 CPU, 8GB RAM × 2 | 4–8 triệu VNĐ |
| PostgreSQL (managed) | db.t3.medium + 100GB SSD | 3–6 triệu VNĐ |
| Redis (managed) | cache.t3.micro (3-node) | 1–2 triệu VNĐ |
| CDN (Cloudflare) | Pro plan | 0.5–1 triệu VNĐ |
| Object Storage (S3) | ~100GB + bandwidth | 1–2 triệu VNĐ |
| Email Service (SES) | ~10,000 emails/tháng | 0.2–0.5 triệu VNĐ |
| Monitoring (Grafana Cloud) | Free tier | 0 |
| SSL Certificate | Let's Encrypt | 0 |
| **Subtotal Infrastructure** | | **9–20 triệu VNĐ** |

### 3.2 Software & Services

| Hạng mục | Chi phí/tháng |
|---|---|
| Error tracking (Sentry) | 0.5–2 triệu VNĐ |
| Uptime monitoring | 0.3–1 triệu VNĐ |
| SMS (OTP, notifications) | 2–5 triệu VNĐ |
| Zalo ZNS | 1–3 triệu VNĐ |
| **Subtotal Software** | **3.8–11 triệu VNĐ** |

### 3.3 Variable Costs (theo doanh số)

| Hạng mục | Tỷ lệ | Ví dụ (100 đơn/ngày, ~5tr/đơn) |
|---|---|---|
| Phí cổng thanh toán VNPAY | ~1.5% | ~7.5 triệu VNĐ/tháng |
| Phí cổng thanh toán MoMo | ~1.5% | ~7.5 triệu VNĐ/tháng |
| Phí vận chuyển GHN/GHTK | 20–45k/đơn (COD) | Đối soát COD, không phải chi phí hệ thống |

### 3.4 Tóm tắt Chi phí Vận hành

| Phương án | Infrastructure | Software | **Tổng cố định/tháng** |
|---|---|---|---|
| MVP (50–100 đơn/ngày) | 9–15 triệu | 4–8 triệu | **13–23 triệu** |
| **Trung bình (khuyến nghị)** | **15–25 triệu** | **8–15 triệu** | **23–40 triệu** |
| Tăng trưởng (500+ đơn/ngày) | 40–80 triệu | 15–30 triệu | **55–110 triệu** |

---

## 4. Vendor Management

### 4.1 Đối tác Thanh toán

| Đối tác | Loại | Phí | Điều kiện | Thời gian setup |
|---|---|---|---|---|
| VNPAY | Cổng thanh toán | 1–2% giao dịch | Cần MST, ĐKKD, web thật | 1–3 tuần |
| MoMo | Ví điện tử | 1–1.5% | Cần MST, ĐKKD | 1–2 tuần |
| ZaloPay | Ví điện tử | 1–1.5% | Cần MST, ĐKKD | 1–2 tuần |
| Kredivo | BNPL | 2–3% | Doanh nghiệp đủ điều kiện | 2–4 tuần |

### 4.2 Đối tác Vận chuyển

| Đối tác | Phạm vi | Phí COD | Phí ship | Webhook |
|---|---|---|---|---|
| GHN | Toàn quốc | 28–32k/đơn | 15–45k | Có |
| GHTK | Toàn quốc | 25–30k/đơn | 15–40k | Có |

**Lưu ý:** Phí thực tế phụ thuộc hợp đồng và volume. Đàm phán giá tốt hơn khi có cam kết volume hàng tháng.

### 4.3 Vendor Management Checklist

- [ ] Ký hợp đồng với ít nhất 2 cổng thanh toán trước Go-live
- [ ] Ký hợp đồng với ít nhất 1 đơn vị vận chuyển trước Go-live
- [ ] Nhận credentials (API key, sandbox) từ từng đối tác
- [ ] Test hoàn chỉnh trên sandbox environment
- [ ] Kế hoạch dự phòng nếu 1 đối tác có downtime

---

## 5. Kế hoạch Mua sắm

### 5.1 Timeline mua sắm

| Hạng mục | Thời điểm | Notes |
|---|---|---|
| Cloud infrastructure | Tuần 1 | Cần ngay cho dev/staging |
| Domain + SSL | Tuần 1 | Cần tên miền trước mọi thứ |
| Đăng ký cổng thanh toán | Tuần 1 | Quá trình duyệt mất 1–3 tuần |
| Ký hợp đồng GHN/GHTK | Tuần 2 | Cần trước khi test shipping |
| Design tools (Figma) | Tuần 1 | Cho Designer |
| Error tracking (Sentry) | Tuần 2 | Trước khi deploy staging |
| Monitoring tools | Tuần 4 | Trước khi deploy staging |

### 5.2 Budget Approval Thresholds

| Số tiền | Approval |
|---|---|
| < 5 triệu VNĐ | PM |
| 5–30 triệu VNĐ | PM + Business Owner |
| > 30 triệu VNĐ | Steering Committee |

---

## 6. ROI Summary

### 6.1 Tổng Chi phí Năm 1

| Hạng mục | Ước tính |
|---|---|
| Chi phí phát triển (phương án trung) | 900 triệu – 1,4 tỷ VNĐ |
| Chi phí vận hành năm 1 (12 tháng × 30–40tr) | 360–480 triệu VNĐ |
| Marketing & SEO content | 60–120 triệu VNĐ |
| **Tổng Chi phí Năm 1** | **1,32–2 tỷ VNĐ** |

### 6.2 Doanh thu Kỳ vọng Năm 1

*Giả định: 50–200 đơn/ngày, giá trị trung bình 8 triệu VNĐ/đơn, lợi nhuận gộp 12–15%*

| Kịch bản | Đơn/ngày | Doanh thu/năm | Lợi nhuận gộp/năm |
|---|---|---|---|
| Thận trọng | 30 | 87,6 tỷ VNĐ | 10–13 tỷ VNĐ |
| Cơ bản | 80 | 233,6 tỷ VNĐ | 28–35 tỷ VNĐ |
| Tích cực | 150 | 438 tỷ VNĐ | 53–66 tỷ VNĐ |

**Break-even:** Ở kịch bản cơ bản, break-even trong 2–3 tháng vận hành.

*Lưu ý: Đây là ước tính dựa trên benchmark thị trường. Kết quả thực tế phụ thuộc vào chiến lược kinh doanh, marketing, và cạnh tranh thị trường.*
