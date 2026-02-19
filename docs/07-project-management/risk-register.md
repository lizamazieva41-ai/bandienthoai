# Risk Register – Sổ Rủi ro Dự án

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  
**Cập nhật:** Mỗi 2 tuần trong Sprint Review  

---

## 1. Risk Assessment Matrix

```
          IMPACT
          │ Thấp    │ Trung bình │ Cao    │ Rất cao
──────────┼─────────┼────────────┼────────┼─────────
Rất cao   │  Trung  │    Cao     │ Nghiêm │ Nghiêm
Cao       │  Thấp   │    Trung   │ Cao    │ Nghiêm
Trung bình│  Thấp   │    Thấp    │ Trung  │ Cao
Thấp      │  Thấp   │    Thấp    │ Thấp   │ Trung
          LIKELIHOOD
```

**Mức rủi ro:**
- 🔴 **Nghiêm trọng** – Cần hành động ngay lập tức
- 🟠 **Cao** – Cần kế hoạch giảm thiểu chi tiết
- 🟡 **Trung bình** – Theo dõi và có kế hoạch dự phòng
- 🟢 **Thấp** – Theo dõi định kỳ

---

## 2. Rủi ro Kỹ thuật

| ID | Rủi ro | Likelihood | Impact | Mức | Biện pháp | Owner |
|---|---|---|---|---|---|---|
| TR-01 | Trễ tích hợp cổng thanh toán (VNPAY/MoMo chậm phê duyệt merchant) | Cao | Cao | 🔴 | Đăng ký ngay tuần 1; có backup phương án (chỉ dùng COD trước) | PM + Kế toán |
| TR-02 | Webhook thanh toán không ổn định (cổng lỗi) | Trung bình | Cao | 🟠 | Implement retry logic; dead letter queue; monitoring real-time | Tech Lead |
| TR-03 | Hiệu năng database kém khi tải cao | Trung bình | Cao | 🟠 | Load testing trước Go-live; indexing strategy; caching layer | Backend Dev |
| TR-04 | Lỗ hổng bảo mật nghiêm trọng trước Go-live | Thấp | Rất cao | 🟠 | SAST/DAST trong CI; security review; pen test | Tech Lead + Security |
| TR-05 | Breaking changes từ API đối tác (GHN/GHTK/VNPAY) | Thấp | Cao | 🟡 | Abstraction layer; version pinning; theo dõi changelog | Backend Dev |
| TR-06 | Data corruption khi migration database | Thấp | Rất cao | 🟠 | Backup trước migration; staging test; rollback plan | DevOps |
| TR-07 | Tích hợp POS phức tạp hơn dự kiến | Cao | Trung bình | 🟡 | Phạm vi giai đoạn 2; PoC trước khi commit | Tech Lead |
| TR-08 | Performance frontend kém (Core Web Vitals fail) | Trung bình | Trung bình | 🟡 | Performance budget trong CI; Lighthouse CI | Frontend Dev |

---

## 3. Rủi ro Nghiệp vụ

| ID | Rủi ro | Likelihood | Impact | Mức | Biện pháp | Owner |
|---|---|---|---|---|---|---|
| BR-01 | Yêu cầu thay đổi nhiều sau khi phát triển | Cao | Cao | 🔴 | Chốt BRD trước Sprint 1; Change Management process nghiêm ngặt | PM + Business Owner |
| BR-02 | Doanh số online thấp hơn kỳ vọng tháng đầu | Cao | Trung bình | 🟠 | SEO từ ngày đầu; không rely on ads ngay; review KPI sau 30 ngày | Business Owner |
| BR-03 | Tỷ lệ hoàn hàng cao (đặt biệt phân khúc trả góp) | Trung bình | Cao | 🟠 | Chính sách đổi trả chặt chẽ; OTP nhận hàng; bằng chứng ảnh | CSKH Lead |
| BR-04 | Thiếu hụt nhân sự kỹ thuật giữa chừng | Trung bình | Cao | 🟠 | Documentation đầy đủ; không single point of knowledge; kế hoạch backup | PM + HR |
| BR-05 | Chi phí vận hành cao hơn ước tính (phí cloud, cổng TT) | Trung bình | Trung bình | 🟡 | Monitor cost weekly; right-sizing instances; negotiate phí với gateway | PM + CFO |
| BR-06 | Đối thủ ra mắt tính năng mới cạnh tranh | Cao | Thấp | 🟢 | Tập trung USP: dịch vụ, bảo hành, IMEI tracking | Business Owner |

---

## 4. Rủi ro Tuân thủ & Pháp lý

| ID | Rủi ro | Likelihood | Impact | Mức | Biện pháp | Owner |
|---|---|---|---|---|---|---|
| CR-01 | Không hoàn thành thủ tục thông báo BCT trước Go-live | Thấp | Cao | 🟡 | Nộp hồ sơ từ tuần 2; track deadline | PM + Legal |
| CR-02 | Vi phạm Nghị định 13/2023 về dữ liệu cá nhân | Thấp | Rất cao | 🟠 | Privacy review; DPA với bên thứ ba; cookie consent | Tech Lead + Legal |
| CR-03 | Khiếu nại người tiêu dùng về sản phẩm lỗi | Trung bình | Trung bình | 🟡 | Chính sách đổi trả rõ ràng; quy trình bảo hành | CSKH Lead |
| CR-04 | PCI DSS violation (lưu dữ liệu thẻ) | Rất thấp | Rất cao | 🟠 | Tokenization; không tự xử lý thẻ; sử dụng cổng TT uy tín | Tech Lead |
| CR-05 | Tranh chấp với carrier về COD | Trung bình | Thấp | 🟢 | Hợp đồng rõ ràng; đối soát daily; escalation procedure | Operations |

---

## 5. Rủi ro Vận hành

| ID | Rủi ro | Likelihood | Impact | Mức | Biện pháp | Owner |
|---|---|---|---|---|---|---|
| OR-01 | DDoS attack vào website | Thấp | Cao | 🟡 | Cloudflare WAF + DDoS protection; rate limiting | DevOps |
| OR-02 | Mất dữ liệu do lỗi database | Rất thấp | Rất cao | 🟠 | Daily backup; WAL archiving; cross-region replication | DevOps |
| OR-03 | Cloud provider outage | Rất thấp | Cao | 🟡 | Multi-AZ deployment; DR plan với RTO 1h | DevOps |
| OR-04 | Third-party service downtime (cổng TT, carrier API) | Trung bình | Trung bình | 🟡 | Circuit breaker; graceful degradation; status monitoring | Tech Lead |
| OR-05 | Fraud: Đặt hàng giả, COD scam | Cao | Trung bình | 🟠 | OTP xác nhận giao hàng; giới hạn COD theo địa chỉ mới; fraud detection | Operations |

---

## 6. Risk Response Strategies

### 6.1 Chiến lược ứng phó

| Chiến lược | Áp dụng khi | Ví dụ |
|---|---|---|
| **Avoid** (Tránh) | Rủi ro quá cao, có thể loại bỏ | Không tự xử lý thẻ, dùng cổng TT |
| **Mitigate** (Giảm thiểu) | Rủi ro cao, không thể tránh | Security review, load testing |
| **Transfer** (Chuyển giao) | Rủi ro có thể bảo hiểm hoặc vendor quản lý | Cyber insurance, managed DB |
| **Accept** (Chấp nhận) | Rủi ro thấp, chi phí giảm thiểu > lợi ích | Minor UI bugs, low-traffic downtime |

### 6.2 Contingency Plans

**TR-01: Cổng thanh toán chậm approve:**
- Fallback: Chỉ hỗ trợ COD và chuyển khoản trong 2 tuần đầu
- Tiếp tục push VNPAY/MoMo trong background
- Communicate với khách hàng về giới hạn tạm thời

**BR-04: Thiếu nhân sự:**
- Danh sách freelancer backup sẵn sàng
- Documentation đầy đủ để onboard nhanh
- Tạm thời giảm scope nếu cần, delay giai đoạn 2

**OR-05: COD fraud:**
- Temporary: Tắt COD cho địa chỉ mới không xác minh
- Require: Xác nhận qua số điện thoại trước khi ship COD
- Blacklist địa chỉ/số điện thoại có lịch sử gian lận

---

## 7. Risk Monitoring

### 7.1 Review Cadence

| Loại review | Tần suất | Participants |
|---|---|---|
| Risk status update | Hàng tuần (Sprint Review) | PM, Tech Lead |
| Risk re-assessment | Hàng tháng | PM, Tech Lead, Business Owner |
| Post-incident risk review | Sau mỗi P1/P2 incident | Full team |
| Go-live risk gate | Trước Go-live | All stakeholders |

### 7.2 Risk Status Tracking

| ID | Trạng thái | Cập nhật lần cuối | Ghi chú |
|---|---|---|---|
| TR-01 | 🔴 Active | 2026-02-19 | Đã gửi hồ sơ VNPAY, chờ duyệt |
| TR-02 | 🟡 Monitored | 2026-02-19 | Retry logic đã implement |
| CR-01 | 🟡 In Progress | 2026-02-19 | Đang chuẩn bị hồ sơ |
| OR-05 | 🟠 Active | 2026-02-19 | Cần implement OTP giao hàng |

---

## 8. Issue Log (Rủi ro đã xảy ra thành sự cố)

| Date | Issue | Impact | Resolution | Preventive Action |
|---|---|---|---|---|
| *(Chưa có sự cố)* | | | | |
