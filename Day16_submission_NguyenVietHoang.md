# Day 16 Submission — Team VinBot (Individual)

## Members

- Nguyễn Việt Hoàng - Product Builder (individual submission)

---

## 1. Idea reframed

Original idea:

> Xây dựng VinBot AI Agent hỗ trợ hậu mãi xe máy điện VinFast theo mô hình đa kênh chat + hotline trợ lý AI: tra cứu bảo hành, chẩn đoán telemetry, tìm xưởng gần nhất và đặt lịch dịch vụ.

Reframed as a product opportunity:

> Khoảng trống hiện tại không nằm ở việc thiếu thông tin, mà nằm ở việc thông tin hậu mãi bị phân mảnh theo từng bước (bảo hành, policy, chẩn đoán, booking) và theo từng kênh tương tác, khiến khách hàng phải tự ghép quy trình. Điều này tạo ra độ trễ phản hồi, tăng phụ thuộc vào tư vấn thủ công và làm trải nghiệm không nhất quán. Cơ hội sản phẩm là tạo một AI service workflow assistant hoạt động xuyên suốt cả chat và hotline, có thể đi từ câu hỏi đến hành động đặt lịch với guardrails. Founding belief: nếu rút ngắn thời gian từ "có vấn đề" đến "có lịch hẹn xác nhận" trên mọi kênh chính, thì cả CSAT của khách hàng và hiệu suất vận hành xưởng đều cải thiện.

---

## 2. Customer / Segment Card

- **Segment name:** Chủ xe máy điện VinFast tại đô thị, đang trong (hoặc cận hết) thời hạn bảo hành, có nhu cầu bảo dưỡng/sửa chữa định kỳ.
- **Operational context:** Người dùng phát sinh sự cố hoặc thắc mắc hậu mãi ngoài giờ cao điểm tổng đài, cần biết quyền lợi và bước xử lý ngay.
- **Recurring workflow:** Nhận tín hiệu bất thường trên xe -> hỏi còn bảo hành/policy gì -> cần chẩn đoán sơ bộ -> tìm xưởng phù hợp -> đặt lịch còn slot.
- **Pain moment:** Khi xe báo lỗi hoặc pin xuống hiệu năng, người dùng không chắc mức độ nghiêm trọng và không biết đi xưởng nào/giờ nào còn trống.
- **Why now:** Tập khách hàng xe điện tăng nhanh, kỳ vọng phản hồi tức thì cao hơn, trong khi kênh tư vấn thủ công khó scale khi lưu lượng câu hỏi lặp lại tăng.
- **Access path:** Nhúng chatbot vào web hậu mãi chính thức, triển khai hotline trợ lý AI tích hợp cùng backend nghiệp vụ, QR tại xưởng dịch vụ, và điều hướng từ kênh CS hiện có (fanpage/app).

One-sentence description:

> Đây là nhóm chủ xe VinFast cần một trợ lý hậu mãi số giúp ra quyết định dịch vụ nhanh và đúng trong các tình huống lỗi/policy lặp lại.

---

## 3. Need Map (2–3 needs)

### Need #1 (priority)

- **Statement (JTBD):** When my vehicle shows warning signs or unusual performance, I want to quickly know warranty eligibility and likely issue severity, so I can decide the next service action without wasting time.
- **Current workaround:** Gọi hotline, hỏi nhóm cộng đồng, tự đọc rời rạc tài liệu/policy rồi tự phán đoán.
- **Pain signal:** Mất thời gian chờ phản hồi, tăng lo lắng vận hành, có nguy cơ xử lý sai mức độ lỗi.
- **Evidence / proxy evidence:** Đề bài chỉ ra câu hỏi policy lặp lại cao và khó tổng hợp dữ liệu kỹ thuật để phản hồi nhanh; đây là proxy cho bottleneck tư vấn thủ công.
- **Why underserved:** Hệ thống hiện tại trả lời theo từng mảng riêng lẻ (policy/kỹ thuật), chưa có một luồng hợp nhất thành quyết định hành động.

### Need #2

- **Statement (JTBD):** When I need maintenance or repair, I want to find a suitable nearby service center with available slots immediately, so I can secure an appointment in one flow.
- **Current workaround:** Tự tìm danh sách xưởng, gọi nhiều nơi để hỏi giờ trống, chốt lịch qua nhiều bước thủ công.
- **Pain signal:** Tăng thời gian hoàn tất đặt lịch, dễ trùng/đứt luồng, trải nghiệm rời rạc giữa tư vấn và booking.
- **Evidence / proxy evidence:** Đề bài nêu rõ luồng tìm xưởng và chốt lịch chưa liền mạch; hệ thống mới phải dùng booking state machine + TTL để tránh xung đột slot.
- **Why underserved:** Công cụ hiện có tập trung cung cấp thông tin, chưa đảm bảo tính nhất quán trạng thái booking theo thời gian thực.

### Need #3 (optional)

- **Statement (JTBD):** When my request is outside AI handling scope, I want a reliable escalation path to human support, so I can still resolve complex issues safely.
- **Current workaround:** Người dùng tự tìm số hotline hoặc thử lại nhiều kênh khác nhau.
- **Pain signal:** Đứt mạch hỗ trợ, tăng frustration khi AI trả lời không đủ phạm vi.
- **Evidence / proxy evidence:** Đề bài quy định guardrails và chuyển hotline 1900 23 23 89 cho case phức tạp.
- **Why underserved:** Nhiều chatbot chỉ dừng ở trả lời Q&A mà thiếu cơ chế handoff minh bạch sang người thật.

---

## 4. Strategy Statement

For VinFast e-motorbike owners in urban areas who are in warranty or near warranty expiry  
who struggle with fragmented after-sales support across policy, diagnostics, and appointment booking,  
our product helps them complete a confident service decision and booking in one seamless interaction  
through an integrated Vietnamese AI assistant across chat and hotline, with tool-calling to warranty lookup, telemetry diagnosis, service-center search, and slot reservation,  
unlike isolated hotline scripts or static FAQ/chat that cannot execute operational actions end-to-end with channel continuity,  
because we can leverage structured after-sales data, booking state control, and domain-specific guardrails for safety.

---

## 5. Moat Hypothesis

**Moat mechanism:** Domain-learning flywheel + workflow embedding in after-sales operations.

If we deploy 50 times in EV after-sales contexts, the following improve:

1. Accuracy of intent-to-tool routing for real customer requests (less fallback, faster resolution).
2. Quality of diagnosis and policy explanation for recurring issue patterns.
3. Booking reliability under concurrent demand via stronger operational rules and exception handling.

Why competitors cannot easily replicate this:

> Lợi thế nằm ở dữ liệu vận hành tích lũy theo workflow hậu mãi thực tế (không chỉ model chung), cùng mức độ nhúng sâu vào quy trình đặt lịch/trạng thái xưởng. Đối thủ có thể sao chép giao diện chatbot, nhưng khó sao chép tốc độ học từ luồng dữ liệu thực và chất lượng phối hợp giữa AI + nghiệp vụ + vận hành.

---

## 6. Initial TAM / SAM / SOM view

Ghi chú phương pháp (để sát thực tế): chưa có số production chính thức, nên market sizing dùng **scenario-based estimation** theo logic vận hành, tách rõ fact và assumption.

**Facts từ đề bài hiện có:**

- Có 3 nhu cầu cốt lõi đã implement: bảo hành, policy/chẩn đoán, đặt lịch.
- Có luồng booking trạng thái `AVAILABLE -> PENDING -> CONFIRMED` và TTL 5 phút để tránh xung đột.
- Có cơ chế guardrails và escalation về hotline cho case ngoài phạm vi.

| Layer | Estimate                                                                                    | Key assumptions                                                                                                                                                                       | Confidence |
| ----- | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------- |
| TAM   | ~$0.3M–$2.0M/year (VN, giá trị kinh tế của AI hậu mãi cho xe máy điện trong scope hiện tại) | A1: 0.6M–1.2M phiên hỏi đáp hậu mãi/năm; A2: 35–55% phiên nằm trong 3 nhóm nhu cầu cốt lõi; A3: giá trị tạo ra mỗi phiên xử lý tốt (giảm tải CS + giảm rớt lịch hẹn) ~$0.8–$3.0/phiên | low        |
| SAM   | ~$0.1M–$0.7M/year (đô thị có mật độ xưởng cao + kênh chat/hotline đã tích hợp)              | S1: 30–45% TAM thuộc địa bàn và hạ tầng rollout giai đoạn đầu; S2: 60–75% scope kỹ thuật hiện tại xử lý ổn trên các case lặp lại                                                      | low-med    |
| SOM   | ~$40K–$180K/year (mục tiêu 12–24 tháng, pilot-first)                                        | O1: chiếm 15–25% SAM ở cụm triển khai đầu; O2: conversion từ tư vấn sang `CONFIRMED` tăng dần nhờ tối ưu intent routing và handoff                                                    | med        |

**Top 3 unknowns requiring further research:**

1. Baseline thật của từng KPI trước AI: AHT, FCR, tỉ lệ booking confirmed, tỉ lệ escalation.
2. Unit economics theo từng phiên xử lý thành công: tiết kiệm vận hành bao nhiêu và giảm rớt lịch bao nhiêu.
3. Chất lượng theo kênh (chat vs hotline): độ chính xác chẩn đoán sơ bộ, CSAT, và tỉ lệ chuyển tuyến đúng người đúng lúc.

**Judgment:**

- [x] Worth pursuing now (pilot-first, đo KPI 8–12 tuần trước khi scale)
- [ ] Worth pursuing but not now (need to validate [...] first)
- [ ] Not worth pursuing as currently framed

---

## 7. Positioning Note (2 sentences)

**What we are:**

> VinBot là AI after-sales workflow agent cho xe máy điện VinFast, giúp khách hàng đi từ câu hỏi bảo hành/lỗi đến lịch hẹn xác nhận trong một luồng liền mạch giữa chat và hotline.

**What we are not / not yet:**

> VinBot chưa phải nền tảng chăm sóc khách hàng toàn năng đa kênh, và hiện chưa xử lý thanh toán, xác thực nâng cao, hay multimodal.

---

## 8. Self-assessment before Day 17

Trong 6 mắt xích (Idea -> Customer -> Need -> Strategy -> Moat -> Market Size), mắt xích nào team đang yếu nhất?

> Mắt xích yếu nhất hiện tại là Market Size vì còn thiếu số liệu thực địa về conversion, retention và economics; phần này mới dừng ở mức giả định có cấu trúc.

Open questions chúng tôi muốn khám phá thêm ở Day 17:

1. MVP nào tạo tác động lớn nhất cho conversion sang booking confirmed trong 2 tuần đầu?
2. Assumption rủi ro cao nhất trong logic giá trị là gì, và nên kiểm chứng bằng experiment nào trước?
3. Cần instrumentation/logging gì để biến mỗi phiên chat thành dữ liệu học cho moat mà vẫn đảm bảo guardrails?
