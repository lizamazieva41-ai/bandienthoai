# Change Management Plan

**Phiên bản:** 1.0.0  
**Ngày:** 2026-02-19  
**Chuẩn tham chiếu:** PMBOK 7th Edition  

---

## 1. Mục tiêu Change Management

Quy trình này đảm bảo mọi thay đổi đối với phạm vi, lịch trình, hoặc ngân sách dự án đều được:
1. **Ghi nhận và đánh giá** đầy đủ tác động
2. **Phê duyệt** bởi người có thẩm quyền phù hợp
3. **Truyền thông** đến tất cả stakeholders liên quan
4. **Triển khai** có kiểm soát và theo dõi

---

## 2. Change Request Process

### 2.1 Quy trình xử lý yêu cầu thay đổi

```mermaid
flowchart TD
  SUBMIT[Gửi Change Request\n(CR Form)] --> PM_REVIEW[PM Review sơ bộ\n24 giờ]
  PM_REVIEW --> CLASSIFY{Phân loại\ntác động}
  
  CLASSIFY -->|Minor\nkhông ảnh hưởng timeline/budget| TECH_ASSESS[Tech Lead đánh giá\ntechnical feasibility]
  CLASSIFY -->|Major\nảnh hưởng timeline/budget| IMPACT_ANAL[Impact Analysis\nfull team]
  
  TECH_ASSESS --> PM_APPROVE{PM Phê duyệt}
  IMPACT_ANAL --> STEERING{Steering Committee\nPhê duyệt}
  
  PM_APPROVE -->|Approved| BACKLOG[Thêm vào\nBacklog sprint tiếp]
  PM_APPROVE -->|Rejected| REJECT_MINOR[Thông báo\nlý do từ chối]
  
  STEERING -->|Approved| REPLAN[Cập nhật Project Plan\n(scope/timeline/budget)]
  STEERING -->|Rejected| REJECT_MAJOR[Thông báo\nlý do từ chối]
  
  BACKLOG --> IMPLEMENT[Implement\ntrong sprint]
  REPLAN --> IMPLEMENT
  IMPLEMENT --> VERIFY[Verify & Close CR]
```

### 2.2 Thời gian xử lý

| Loại CR | SLA Review | SLA Quyết định |
|---|---|---|
| Minor (< 1 SP effort) | 1 ngày làm việc | 2 ngày làm việc |
| Major (> 1 SP, ảnh hưởng timeline) | 2 ngày làm việc | 5 ngày làm việc |
| Critical (ảnh hưởng Go-live) | 4 giờ làm việc | 1 ngày làm việc |

---

## 3. Change Request Form

```markdown
## Change Request #CR-YYYY-NNN

**Ngày gửi:** DD/MM/YYYY  
**Người gửi:** [Tên, vai trò]  
**Ưu tiên:** Critical / High / Medium / Low  

### 1. Mô tả thay đổi
[Mô tả rõ thay đổi muốn thực hiện]

### 2. Lý do / Business Justification
[Tại sao cần thay đổi này?]

### 3. Phạm vi thay đổi
- [ ] Scope (tính năng mới/thay đổi tính năng)
- [ ] Timeline (ảnh hưởng đến Go-live date)
- [ ] Budget (tăng/giảm chi phí)
- [ ] Technical architecture

### 4. Tác động ước tính
- **Story Points:** X SP
- **Ảnh hưởng timeline:** +/- X ngày
- **Ảnh hưởng ngân sách:** +/- X triệu VNĐ
- **Rủi ro:** [Liệt kê rủi ro nếu có]
- **Dependencies:** [Các feature/module bị ảnh hưởng]

### 5. Phân tích lợi ích
[Lợi ích khi thực hiện thay đổi này]

### 6. Phân tích rủi ro khi không thực hiện
[Hậu quả nếu không thực hiện thay đổi]

### 7. Đề xuất giải pháp
[Giải pháp kỹ thuật đề xuất]

---
**Quyết định:**  
- [ ] Approved  
- [ ] Approved với điều kiện: ___  
- [ ] Rejected – Lý do: ___  
- [ ] Deferred đến Phase: ___  

**Người phê duyệt:** ___  
**Ngày phê duyệt:** ___  
```

---

## 4. Change Categories

### 4.1 Phân loại theo tác động

| Category | Mô tả | Approval Authority | Ví dụ |
|---|---|---|---|
| **Minor** | Thay đổi nhỏ, không ảnh hưởng timeline/budget | PM | Thay đổi label UI, thêm field nhỏ |
| **Moderate** | Ảnh hưởng 1–3 sprint, tăng effort ≤ 20% | PM + Tech Lead | Thêm tính năng nhỏ ngoài scope |
| **Major** | Ảnh hưởng Go-live date hoặc tăng budget > 20% | Steering Committee | Thay đổi kiến trúc, thêm module mới |
| **Emergency** | Lỗi critical cần fix ngay trong production | Tech Lead + PM | Security hotfix, payment bug |

### 4.2 Emergency Changes

Emergency changes (hotfix production) có quy trình rút gọn:

```
1. Tech Lead xác nhận: "Cần emergency change vì [lý do]"
2. PM approve qua Slack/phone
3. Implement + test trong < 4 giờ
4. Deploy với change record
5. Post-deployment review trong 24 giờ
6. Retrospective: Làm thế nào để tránh lần sau?
```

---

## 5. Approval Authority Matrix

| Loại thay đổi | PM | Tech Lead | Business Owner | Steering Committee |
|---|---|---|---|---|
| Minor (< 4 giờ effort) | ✅ | C | I | I |
| Moderate (4h–3 ngày) | A | R | C | I |
| Major (> 3 ngày hoặc budget impact) | C | C | C | ✅ |
| Architecture change | C | A | C | ✅ |
| Emergency hotfix | A | R | I | I |
| Scope reduction (cut feature) | C | C | A | ✅ |

*A=Accountable, R=Responsible, C=Consulted, I=Informed*

---

## 6. Impact Assessment Template

Khi đánh giá impact của thay đổi, team phải phân tích:

### 6.1 Technical Impact

| Aspect | Câu hỏi cần trả lời |
|---|---|
| Scope | Thay đổi có phá vỡ tính năng hiện có không? |
| Database | Cần migration? Breaking schema change? |
| API | Có breaking change trong API contract? |
| Integration | Ảnh hưởng đến tích hợp bên thứ ba? |
| Security | Tạo ra rủi ro bảo mật mới? |
| Performance | Ảnh hưởng đến hiệu năng hệ thống? |
| Testing | Test effort thêm bao nhiêu? |

### 6.2 Business Impact

| Aspect | Câu hỏi cần trả lời |
|---|---|
| Timeline | Ảnh hưởng đến Go-live date? |
| Budget | Tăng chi phí bao nhiêu? |
| Revenue | Ảnh hưởng đến doanh thu ước tính? |
| Customer | Ảnh hưởng đến trải nghiệm khách hàng? |
| Compliance | Tạo ra vấn đề pháp lý? |

---

## 7. Scope Freeze Policy

### 7.1 Giai đoạn MVP (Sprint 1 – Sprint 8)

- **Scope freeze** cho MVP bắt đầu từ Sprint 3
- Sau scope freeze, mọi CR đều phải qua full approval process
- Yêu cầu mới không urgent sẽ được đưa vào Giai đoạn 2 backlog
- PM có quyền từ chối CR nếu sẽ làm trễ Go-live

### 7.2 Feature Flags

Với các thay đổi lớn sau Go-live, sử dụng feature flags để:
- Deploy code mà không activate tính năng
- A/B testing
- Rollback nhanh nếu tính năng có vấn đề

---

## 8. Change Log

| CR ID | Ngày | Mô tả | Category | Status | Impact |
|---|---|---|---|---|---|
| CR-2026-001 | 2026-02-19 | Khởi tạo change management process | N/A | Active | N/A |

*Log này được cập nhật mỗi khi có CR mới hoặc thay đổi trạng thái.*
