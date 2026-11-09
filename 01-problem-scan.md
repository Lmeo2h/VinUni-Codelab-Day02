# Lab 02 — Problem Scan & Quick Assessment (Vin Smart Future)

## 🏛️ Bối cảnh
Đóng vai trò là **AI Product Engineer** tại **Vin Smart Future**, tôi tiến hành khảo sát các quy trình vận hành thực tế tại các công ty thành viên thuộc tập đoàn Vingroup (Xanh SM, VinFast, Vinhomes, Vinmec, Vinpearl) để nhận diện các điểm nghẽn (bottlenecks) và đánh giá mức độ khả thi khi ứng dụng AI.

---

## 🔍 Phase 1 — SCAN: Tìm kiếm cơ hội vận hành (5+ Bài toán qua 4 Lenses)

| # | Subsidiary | Lens | Mô tả ngắn bài toán & Điểm nghẽn (Bottleneck) |
|---|---|---|---|
| 1 | **Xanh SM (GSM)** | **Tốn thời gian (Time-consuming)** | Điều phối viên (Dispatcher) phải xử lý thủ công các cuộc gọi báo sự cố cạn pin khẩn cấp của tài xế thực địa: tra cứu GPS, tìm trụ sạc VinFast trống phù hợp và gõ tin nhắn chỉ đường (tốn ~15 phút/lượt). |
| 2 | **Vinhomes** | **Lặp lại (Repetitive)** | Ban quản lý tòa nhà phải đọc, phân loại thủ công hàng trăm phản ánh mỗi ngày trên App Vinhomes Resident (mất nước, hỏng thẻ thang máy, tiếng ồn, đăng ký thi công) để chuyển về bộ phận kỹ thuật/an ninh tương ứng (mất 4–12 giờ phản hồi). |
| 3 | **VinFast** | **AI có thể tốt hơn (AI-upgrade)** | Cố vấn dịch vụ tại xưởng dịch vụ VinFast tiếp nhận mô tả lỗi xe bằng ngôn ngữ đời thường từ chủ xe (VD: *"xe đi qua gờ giảm tốc kêu lục cục ở gầm trước"*) và phải tra cứu thủ công để quy đổi sang mã lỗi kỹ thuật ban đầu (tốn 15–20 phút/xe). |
| 4 | **Vinmec** | **Pain từ người khác (Stakeholder Pain)** | Bác sĩ nội trú tốn 20–30 phút/bệnh nhân để tổng hợp kết quả xét nghiệm, đơn thuốc và ghi chú lâm sàng thành bản Tóm tắt hồ sơ xuất viện (Discharge Summary) dễ hiểu cho bệnh nhân, gây quá tải giờ hành chính. |
| 5 | **Vinpearl** | **Tốn thời gian (Time-consuming)** | Nhân viên Sales B2B phải đọc các email đặt phòng đoàn (Group Booking) dài và phức tạp từ đại lý du lịch (TA), bóc tách thủ công số lượng phòng/loại giường/ngày lưu trú để kiểm tra quỹ phòng trống (mất 25–30 phút/email). |
| 6 | **Xanh SM (GSM)** | **Pain từ người khác (Stakeholder Pain)** | Đội vận hành phải nghe lại ghi âm cuộc gọi tổng đài và đọc ghi chú rời rạc của tài xế để phân tích nguyên nhân khách hủy chuyến vào giờ cao điểm, dẫn đến việc phát hiện lỗi định vị GPS chậm trễ. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Chọn Top 3 bài toán có tác động vận hành rõ rệt nhất: **#1 (Xanh SM - Điều phối sự cố cạn pin)**, **#2 (Vinhomes - Phân loại phản ánh cư dân)**, và **#4 (Vinmec - Tóm tắt hồ sơ xuất viện)**.

### Card #1 — Xanh SM: Điều phối cứu hộ & trạm sạc khi xe cạn pin thực địa

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                   │
│                                                                         │
│ Bài toán (1 câu): Tài xế Xanh SM báo sự cố cạn pin thực địa cần điều    │
│ phối viên tìm trụ sạc trống gần nhất hoặc gọi xe cứu hộ pin lưu động.   │
│ Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  [ ] Vinmec  │
│                                                                         │
│ Ai đang đau (Actor)? Tài xế (chờ lâu trên đường), Điều phối viên        │
│ (quá tải tra cứu chéo nhiều màn hình vào giờ cao điểm).                 │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Tài xế gọi tổng đài báo pin yếu/sắp sập nguồn                      │
│   ──> 2. Điều phối viên tra cứu tọa độ GPS xe trên bản đồ nội bộ        │
│   ──> 3. Mở Dashboard trạm sạc VinFast lọc trụ trống khớp dòng xe       │
│   ──> 4. Gõ tin nhắn chỉ dẫn lộ trình gửi qua App Tài xế                │
│   ──> 5. Gọi đội xe cứu hộ pin lưu động nếu pin < 5% và trạm ở quá xa   │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 10 - 12 phút/lượt)       │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & 4                        │
│ (Rule-based lọc trạm/kiểm tra ngưỡng pin < 5% + LLM soạn nháp tin nhắn) │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   - Giảm thời gian xử lý trung bình từ 15 phút ──> dưới 3 phút/lượt.    │
│   - 100% ca pin nguy kịch (< 5%, trạm > 5km) kích hoạt cứu hộ lưu động. │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent     │
└─────────────────────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                   │
│                                                                         │
│ Bài toán (1 câu): Phân loại tự động các phản ánh/khiếu nại đa dạng của  │
│ cư dân trên App Vinhomes Resident và soạn nháp phản hồi ban đầu.        │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  [ ] Vinmec  │
│                                                                         │
│ Ai đang đau (Actor)? Nhân viên CSKH Ban quản lý (đọc thủ công hàng trăm │
│ ticket/ngày), Cư dân (phải chờ đợi phản hồi lâu).                       │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Cư dân gửi ticket kèm văn bản/hình ảnh lên App Vinhomes Resident   │
│   ──> 2. Nhân viên CSKH đọc hiểu nội dung, xác định mức độ khẩn cấp     │
│   ──> 3. Gán nhãn thủ công và chuyển cho bộ phận (Kỹ thuật/An ninh/VS)  │
│   ──> 4. Soạn tin nhắn xác nhận thời gian xử lý gửi lại cho cư dân      │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 4 (⏱ 8 - 10 phút/ticket)      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2, 3 & 4                     │
│ (LLM phân loại intent, gắn mức độ ưu tiên P0-P3 và draft câu trả lời)   │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   - Giảm thời gian phân loại & phản hồi đầu tiên từ 10 phút ──> < 1 phút│
│   - Độ chính xác khi điều hướng đúng phòng ban đạt >= 92%.              │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent     │
└─────────────────────────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                   │
│                                                                         │
│ Bài toán (1 câu): Hỗ trợ bác sĩ tổng hợp bệnh án điện tử thành bản      │
│ tóm tắt xuất viện và hướng dẫn chăm sóc tại nhà dễ hiểu cho bệnh nhân.  │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  [x] Vinmec  │
│                                                                         │
│ Ai đang đau (Actor)? Bác sĩ điều trị (tốn thời gian gõ văn bản hành     │
│ chính), Bệnh nhân (khó hiểu các thuật ngữ y khoa viết tắt).             │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Bác sĩ mở hệ thống HIS xem lại toàn bộ lịch sử khám & xét nghiệm   │
│   ──> 2. Tổng hợp chẩn đoán, phương pháp điều trị và đơn thuốc          │
│   ──> 3. Gõ tay bản hướng dẫn theo dõi tại nhà và dấu hiệu tái khám     │
│   ──> 4. Bác sĩ ký xác nhận và in giao cho bệnh nhân làm thủ tục ra viện│
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 20 - 25 phút/hồ sơ)      │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3                        │
│ (LLM trích xuất dữ liệu từ HIS và sinh bản nháp hướng dẫn xuất viện)    │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   - Giảm thời gian bác sĩ hoàn tất tóm tắt xuất viện từ 25 phút ──> 5p  │
│   - 100% bản tóm tắt phải qua Bác sĩ duyệt (HITL) trước khi in.         │
│                                                                         │
│ Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM Feature  [ ] Agent     │
└─────────────────────────────────────────────────────────────────────────┘