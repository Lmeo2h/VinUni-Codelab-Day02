# Lab 02 · Problem Scan
## Vinhomes AI Resident Complaint Router

Các quy trình là tình huống mô phỏng dựa trên đề bài và Inspiration Kit. Thời gian hiện tại là **mốc giả định**. Các ngưỡng thành công là **KPI mục tiêu**. Chưa có khảo sát thực địa, số liệu vận hành đã xác minh hoặc kết quả đo KPI.

## Phase 1 — SCAN

| # | Công ty | Lens | Actor | Bottleneck | Cơ hội cải thiện |
|---|---|---|---|---|---|
| 1 | Vinhomes | Repetitive, Time-consuming | CSKH/Ban quản lý | Đọc, tra bộ phận và soạn phản hồi lặp lại qua nhiều kênh | Trích xuất dữ kiện, đối chiếu bảng routing và tạo nháp để duyệt |
| 2 | Vinpearl | Stakeholder Pain, Time-consuming | Quản lý khách sạn, nhân viên trải nghiệm khách | Đọc nhận xét rải rác và dễ bỏ sót phàn nàn cần xử lý | Gom chủ đề, trích dẫn nhận xét gốc, đề xuất thứ tự đọc |
| 3 | VinFast | AI-upgrade, Stakeholder Pain | Cố vấn dịch vụ, kỹ thuật viên | Mô tả triệu chứng khó đối chiếu tài liệu và phải hỏi lại | Chuẩn hóa triệu chứng và gợi ý câu hỏi từ tài liệu đã duyệt |
| 4 | VinUni | Repetitive, AI-upgrade | Trợ giảng, sinh viên | Trợ giảng giải thích nhiều lần cùng một nhóm lỗi bài lab | Gom lỗi theo kết quả test và soạn nháp giải thích để duyệt |
| 5 | Vinhomes | Stakeholder Pain, Repetitive | Cư dân, nhân viên hỗ trợ thẻ xe | Hồ sơ thiếu trường hoặc chọn sai thủ tục, gây trao đổi nhiều vòng | Kiểm tra biểu mẫu và hướng dẫn hồ sơ theo loại yêu cầu |
| 6 | Vinpearl | Time-consuming | Nhân viên đặt phòng đoàn | Đọc email nhiều phòng, nhiều mốc rồi nhập lại dữ kiện | Trích xuất yêu cầu và xác thực ngày, số lượng trước khi tra tồn phòng |

## Phase 2 — QUICK-ASSESS

### Card 1 — Vinhomes Complaint Routing

| Trường | Nội dung |
|---|---|
| Problem | CSKH đọc và điều hướng phản ánh từ ứng dụng, hotline hoặc biểu mẫu, dễ tốn thời gian tra cứu hoặc chuyển sai bộ phận. |
| Subsidiary | Vinhomes |
| Actor | CSKH/Ban quản lý phân luồng. Cư dân chờ xác nhận và bộ phận nghiệp vụ chờ hồ sơ đủ dữ kiện. |
| Current workflow | 1. Nhận phản ánh. → 2. Kiểm tra và bổ sung thông tin. → 3. Đọc và phân loại. → 4. Tra cứu và chuyển bộ phận. → 5. Soạn xác nhận và cập nhật trạng thái. Thẻ gộp hai bước cuối của sơ đồ chi tiết. |
| Bottleneck và thời gian | Phân loại 5 phút, tra cứu/chuyển 4 phút, soạn xác nhận 3 phút. Tổng 12/18 phút, khoảng 66,7% thời gian xử lý ban đầu. |
| AI opportunity | Hiểu tiếng Việt tự do, trích xuất dữ kiện, phân loại và soạn nháp. Luật ánh xạ hàng đợi và sàng lọc tín hiệu khẩn cấp rõ ràng. |
| Success metric | Median minh họa 18 → dưới 5 phút/ticket. Routing accuracy ≥90%, recall khẩn cấp ≥95% trên bộ gán nhãn. Nhân viên xem xét 100% ca khẩn cấp hoặc thiếu dữ kiện quan trọng. 0 thao tác gửi, phân công hoặc đóng ticket do AI tự thực hiện. |
| Quick Architecture | **Rule + LLM Feature.** Luật xác thực đích. LLM phân tích và soạn nháp. |
| Rủi ro và giới hạn | Bỏ sót nguy hiểm, chọn sai hàng đợi, lộ dữ liệu hoặc hứa bồi thường. Giới hạn dữ liệu gửi mô hình, ưu tiên chuyển người và yêu cầu phê duyệt trước mọi hành động. |

### Card 2 — Vinpearl Review Analyzer

| Trường | Nội dung |
|---|---|
| Problem | Nhân viên khó tổng hợp nhận xét từ nhiều kênh và nhận ra chủ đề cần ưu tiên theo từng cơ sở. |
| Subsidiary | Vinpearl |
| Actor | Nhân viên trải nghiệm khách tổng hợp. Quản lý cơ sở xác minh và xử lý. |
| Current workflow | 1. Gom nhận xét. → 2. Loại trùng và xác định cơ sở. → 3. Đọc và gán chủ đề. → 4. Lập báo cáo để quản lý duyệt. |
| Bottleneck và thời gian | Đọc và gán chủ đề khoảng 45 phút/lô 30 nhận xét (ASSUMPTION). |
| AI opportunity | Gắn nhãn nhiều chủ đề, trích đoạn làm căn cứ và tạo báo cáo nháp theo cơ sở. |
| Success metric | Dưới 20 phút/lô 30 nhận xét kể cả duyệt. Macro-F1 chủ đề ≥0,85 trên bộ gán nhãn. |
| Quick Architecture | **LLM Feature** chạy theo lô, kèm quy tắc loại trùng. |
| Rủi ro và giới hạn | Mỉa mai, trùng lặp, sai cơ sở và thông tin cá nhân. Dẫn nguồn về nhận xét gốc, yêu cầu quản lý duyệt. Không tự đăng trả lời. |

### Card 3 — VinFast Vehicle Fault Assistant

| Trường | Nội dung |
|---|---|
| Problem | Mô tả lỗi xe bằng tiếng Việt tự do thiếu chi tiết, làm chậm việc sàng lọc và chuyển kỹ thuật viên. |
| Subsidiary | VinFast |
| Actor | Cố vấn dịch vụ tiếp nhận và kỹ thuật viên xác minh. |
| Current workflow | 1. Ghi triệu chứng. → 2. Hỏi điều kiện phát sinh và đèn báo. → 3. Tra tài liệu theo dòng xe. → 4. Ghi hồ sơ và chuyển kỹ thuật viên. |
| Bottleneck và thời gian | Hỏi lại và tra tài liệu khoảng 12 phút/lượt (ASSUMPTION). |
| AI opportunity | Trích xuất dấu hiệu, nháp câu hỏi làm rõ và tìm mục liên quan trong tài liệu đã duyệt. |
| Success metric | Bước hỏi/tra dưới 7 phút/lượt. Ít nhất 90% hồ sơ đủ trường bắt buộc trên bộ gán nhãn. |
| Quick Architecture | **Rule + LLM Feature.** Luật sàng lọc nguy hiểm. LLM hỗ trợ trích xuất và tra cứu. |
| Rủi ro và giới hạn | Nhầm dòng xe hoặc suy diễn chẩn đoán. Không hướng dẫn sửa chữa nguy hiểm hoặc xác nhận xe an toàn. Ca rủi ro phải do kỹ thuật viên tiếp nhận. |

### So sánh và chọn bài toán

| Tiêu chí | Vinhomes | Vinpearl | VinFast |
|---|---|---|---|
| Đơn vị đo | Ticket ban đầu | Lô nhận xét | Lượt sàng lọc |
| Giá trị kỳ vọng | Giảm thời gian phân luồng và chuyển nhầm | Rút ngắn tổng hợp xu hướng | Giảm hỏi lại và tra tài liệu |
| Dữ liệu tối thiểu | Ticket mô phỏng, nhãn khẩn cấp, bảng hàng đợi | Nhận xét có nguồn, cơ sở, nhãn chủ đề | Tài liệu đúng phiên bản xe, triệu chứng, nhãn kỹ thuật |
| Rủi ro cần kiểm soát | Bỏ sót nguy hiểm, lộ dữ liệu, thao tác trái phép | Sai ngữ cảnh hoặc sai cơ sở | Chẩn đoán sai, ảnh hưởng an toàn |
| Phạm vi prototype | Hàng đợi giả lập, duyệt toàn bộ | Chạy ngoại tuyến theo lô | Phụ thuộc tài liệu và người xác minh chuyên môn |

Ưu tiên **Vinhomes Complaint Routing** vì phạm vi dừng ở xử lý ban đầu, có baseline giả định phân rã theo bước và có thể so sánh luật thuần với luật + LLM trên cùng bộ tình huống. Vinpearl thiên về tổng hợp theo lô. VinFast cần tài liệu đúng phiên bản và kỹ thuật viên xác minh. Việc xác nhận dữ liệu, bảng routing và nhu cầu vận hành nằm ở Phase 5.

Xem [báo cáo Deep Dive](02-deep-dive-report.md) và [sơ đồ Current-State](04-workflow-diagram.png).
