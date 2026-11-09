# Lab 02 · AI Log & Reflection
## Vinhomes AI Resident Complaint Router

Nhật ký này ghi lại các yêu cầu và thao tác đã diễn ra trong cuộc trò chuyện. Các yêu cầu dài được trích hoặc tóm tắt có đánh dấu. Không có ý kiến giảng viên, kết quả họp nhóm hay dữ liệu khảo sát để ghi nhận.

## Phase 6 — Mục tiêu và prompt thực sự đã dùng

| Lượt | Yêu cầu đã gửi | Công việc và kết quả quan sát được |
|---|---|---|
| System Instruction mẫu | “thực hiện cho tôi file nằm trong folder starter-code” kèm ảnh Task 1 | AI viết system prompt cho ví dụ GSM. Mọi phản hồi phải gắn `[DRAFT_ONLY]`; pin dưới 5% yêu cầu đề xuất xe sạc di động. Đã kiểm tra cú pháp Python. |
| Tích hợp Gemini mẫu | “thuc hien va check cho toi” kèm ảnh Task 2 và cách chạy | AI triển khai `evaluate_prompt()` và kiểm tra thẻ ở đầu phản hồi. Hai ca dùng phản hồi giả lập đã qua kiểm tra lời gọi SDK. Lần chạy thật ở lượt đó dừng vì thiếu biến API key. |
| Báo cáo Vinhomes | Trích: “Hoàn thiện các tài liệu nộp bài cho dự án: ‘Vinhomes AI Resident Complaint Router — Hệ thống hỗ trợ phân loại và điều hướng phản ánh cư dân bằng AI’.” Yêu cầu kèm baseline sáu bước, KPI giả định, sơ đồ và đối chiếu rubric. | AI đối chiếu README/worksheet với starter và autograder, soạn ba file Markdown và dựng sơ đồ Current-State. Đề xuất Rule + LLM Feature, HITL và tách quyết định prototype khỏi production. |

Hai lượt đầu dùng tình huống **GSM**. Phản hồi giả lập từ các lượt đó chỉ kiểm tra tích hợp SDK, chưa đo khả năng giữ ranh giới của Gemini và không phải bằng chứng cho Vinhomes. Ở lượt soạn báo cáo, ba ca Phase 4 mới là đặc tả. Lượt triển khai tiếp theo bổ sung ca kiểm thử vào code.

## Phản biện và thay đổi khi tự kiểm tra tài liệu

| Điểm cần phản biện | Thay đổi trong tài liệu |
|---|---|
| Một số tổng hợp dễ diễn giải 18 phút như median thực đo hoặc 30 giờ công/ngày như lợi ích đã thu được. | Giữ nhãn ASSUMPTION, trình bày phép tính và yêu cầu đo lại baseline thủ công. |
| Chuỗi nháp có thể bị coi như một lệnh thực thi. Thẻ `[DRAFT_ONLY]` chỉ là nội dung văn bản. | Tách đề xuất, quyền duyệt và thao tác hệ thống. Model không có quyền gửi/phân công/đóng. Log phải đối chiếu được người duyệt với phiên bản đề xuất. |
| Luật từ khóa có thể bỏ sót tiếng Việt không dấu hoặc cách diễn đạt gián tiếp. Ngược lại, ưu tiên do model tạo có thể sai. | Giữ cờ khẩn do luật đặt, cho phép model nâng ca nghi ngờ để người xem, dùng test gán nhãn để đo recall và theo dõi dương tính giả. |
| Một ticket thiếu vị trí vẫn có thể mô tả nguy hiểm trực tiếp. | Chạy sàng lọc khẩn ngay cả khi thiếu trường. Handoff khẩn không chờ model hoặc dữ kiện bổ sung. |
| Pass kiểm tra file hay chuỗi Passed/Failed chưa chứng minh nghiệp vụ đạt chuẩn. | Tách kiểm tra tự động khỏi G1–G4 và ghi I2 Vinhomes chưa xác nhận. Không có lỗi mô hình Vinhomes quan sát được để ghi thành hallucination thực tế. |

## Giả định cần kiểm chứng

- Đo thời gian theo bước và median từ nhật ký thao tác thật, tách thời gian chờ và khắc phục. Kiểm tra lưu lượng 100 ticket/ngày và khác biệt theo kênh.
- Xác nhận danh mục, bảng chuyển theo khu/tòa, tiêu chí ưu tiên và trách nhiệm người trực với CSKH và Ban quản lý.
- Xác nhận khả năng khử định danh, quyền dữ liệu, chế độ lưu và phạm vi người xem log trước khi thử dữ liệu thật.
- Đo chi phí theo ticket, công duyệt và tỷ lệ sửa đích để so sánh với luật thuần.
- Xác nhận với giảng viên cách nộp prototype Vinhomes khi bộ kiểm tra còn gắn với GSM.

## Bài học về operational boundary

Ranh giới cần có ở cả prompt, logic ứng dụng và quyền thao tác. Chỉ thị yêu cầu nháp giúp kiểm tra định dạng. Phân quyền, điểm duyệt, nhánh dự phòng và log mới cho phép xác minh ai đã thực hiện hành động. Ca khẩn phải có đường đến người xử lý ngay cả khi model không trả lời.

## Kiểm tra cuối lượt tài liệu

- Autograder phần A tìm thấy đủ ba file Markdown và PNG (5/5 cho kiểm tra hiện diện). Mục này chưa chấm chất lượng nội dung theo G1–G4.
- Ba kiểm tra tĩnh của code GSM đạt. Hai kiểm tra chạy và kiểm tra phản hồi chưa đạt. Chạy trực tiếp cho thấy môi trường thiếu API key. Không có kết quả Gemini Vinhomes.
- Ảnh PNG đã mở và kiểm tra trực quan ở kích thước 2.600 × 1.752 px. Tệp draw.io đã được đọc và kiểm tra cấu trúc XML.

## Lượt triển khai Phase 4 Vinhomes

- **Prompt thực tế đã dùng:** yêu cầu “Tiếp tục dự án Vinhomes AI Resident Complaint Router” và xây prototype Google Gemini SDK theo ba lớp. Yêu cầu gồm taxonomy, sáu ca đối kháng, ít nhất bốn ca thông thường, pytest offline và giải trình các kiểm tra hardcode GSM.
- **Thay đổi sau kiểm tra:** system prompt tiếng Việt giới hạn vai trò, coi nội dung phản ánh là dữ liệu và yêu cầu JSON có thẻ nháp. Python kiểm tra whitelist, cờ duyệt và mẫu câu nháp, sàng lọc tín hiệu khẩn trước khi kiểm tra thiếu dữ kiện, đồng thời phân biệt đề xuất từ luật, từ model và từ fallback. Các mẫu câu nháp cố định giúp ngăn lịch hẹn hoặc cam kết tự phát trong phần gửi cho cư dân.
- **Đã chạy:** pytest offline đạt 57 ca. Khi chạy script, hai ca luật (điện giật và thiếu vị trí) đạt. Mười ca Gemini được đánh dấu SKIPPED vì thiếu `GEMINI_API_KEY`. Không có phản hồi Gemini thực hoặc kết quả live để lưu.
- **Autograder:** 7,50/10, exit 1. Bốn file đều hiện diện, kiểm tra SDK và danh sách ca đạt. Kiểm tra prompt đòi từ khóa GSM, hai kiểm tra thực thi/kết quả chưa đạt khi thiếu khóa. Giữ nguyên bộ chấm.
- **Cần kiểm chứng:** độ bao phủ cụm khẩn, PII ngoài mẫu regex, khả năng gọi model với tài khoản thực và kết quả trên tập gán nhãn. Chưa có review từ giảng viên hoặc bộ phận vận hành.

## Lượt audit QA

- **Yêu cầu đã dùng:** “Hãy audit toàn bộ workspace của dự án” và ưu tiên sửa lỗi trước khi kết luận. Audit đối chiếu từng tiêu chí G1–G4 và I1–I3 với tài liệu và kết quả chạy.
- **Lỗi thực sự phát hiện:** ba ca hồi quy ban đầu không đạt. Hai câu báo cháy hoặc khói ngắn bị chuyển sang yêu cầu bổ sung dữ kiện. Một tóm tắt mô phỏng có dấu hiệu điện giật bị đưa về kiểm duyệt mức MEDIUM khi kết quả đề xuất LOW. Đã bổ sung mẫu cháy, khói và giữ mức EMERGENCY khi nhánh dự phòng phát hiện xung đột ưu tiên trong tóm tắt hợp lệ.
- **Kết quả sau sửa:** kiểm tra cú pháp đạt. Pytest đạt 60/60. Hai ca luật trong script đạt. Mười ca Gemini bỏ qua vì thiếu khóa. Autograder nguyên bản ghi 7,50/10 và mã thoát 1. Đây là lỗi logic Python được tái hiện bằng kiểm thử, chưa có phản hồi thật của Gemini để đánh giá hallucination.
- **Kiểm tra sơ đồ và Git:** ảnh PNG 2.600 × 1.752 px mở được và hiển thị đầy đủ. ZIP cũ có môi trường ảo nhưng thiếu bốn file nộp chính, nên đã đưa ZIP vào danh sách bỏ qua của Git.

### Lần chạy Gemini từ PowerShell của sinh viên

Kết quả lưu cục bộ và ảnh terminal xác nhận: với `gemini-2.5-flash`, 10 ca Gemini nhận HTTP 404. Khi đổi `GEMINI_MODEL` sang `gemini-3.1-flash-lite`, 8/10 ca Gemini và 2/2 ca luật đạt. Ca hoàn phí bị bộ kiểm tra từ chối (`invalid_output`). Nhật ký chỉ lưu mã lỗi nên chưa xác định được trường vi phạm. Ca bãi xe nhận HTTP 504. Chưa dùng lần chạy này để kết luận KPI vận hành.

Đã làm rõ cách viết tóm tắt trung lập cho phản ánh về phí và bổ sung tùy chọn `--case` để kiểm tra lại từng ca. Sau khi làm rõ thêm cách phân loại lỗi thẻ xe, lần chạy đầy đủ được lưu với `gemini-3.1-flash-lite` ghi nhận **10/10 ca Gemini và 2/2 ca luật đạt**. Pytest offline đạt 60/60. Theo yêu cầu cuối, script hiện mặc định in đầu vào, phản hồi đã kiểm tra và kết quả từng ca.

## Tự phản ánh của sinh viên

Khi làm lab, tôi dùng AI để so sánh cách phân luồng bằng luật với cách dùng mô hình ngôn ngữ. Tôi chọn dùng luật cho tín hiệu khẩn rõ ràng và bảng ánh xạ bộ phận, còn Gemini hỗ trợ hiểu mô tả tiếng Việt. Tôi giữ 18 phút/ticket làm mốc giả định để thiết kế bài toán. Muốn kết luận công cụ giúp giảm thời gian, tôi cần đo quy trình thủ công và quy trình có hỗ trợ trên cùng một bộ hồ sơ.

Tôi chạy prototype trên PowerShell. Gemini 2.5 Flash trả HTTP 404. Sau khi đổi sang Gemini 3.1 Flash-Lite, ca hoàn phí bị bộ kiểm tra từ chối và ca bãi xe từng gặp HTTP 504. Tôi chạy lại từng ca để tách lỗi dịch vụ khỏi sai lệch phân loại. Sau khi làm rõ cách tóm tắt yêu cầu về phí và cách xếp lỗi đầu đọc thẻ vào nhóm PARKING, lần chạy đầy đủ ghi nhận 10 ca Gemini và 2 ca luật đều đạt. Kết quả này chỉ áp dụng cho các ca mô phỏng đã chạy.

Điểm tôi rút ra là nhãn [DRAFT_ONLY] giúp người đọc nhận biết bản nháp, còn bộ kiểm tra và quyền thao tác mới quyết định hệ thống có thể làm gì. Tôi giữ bước nhân viên duyệt và chuyển kiểm duyệt khi phản hồi sai định dạng hoặc yêu cầu vượt quyền. Nếu tiếp tục phát triển, tôi sẽ kiểm tra thêm các mô tả khẩn ngắn, không dấu hoặc dễ nhầm nhóm trước khi đánh giá khả năng triển khai.
