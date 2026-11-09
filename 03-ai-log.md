# QUÁ TRÌNH SỬ DỤNG AI TRONG TÌM KIẾM VÀ ĐÁNH GIÁ BÀI TOÁN VẬN HÀNH TẠI VINGROUP

Trong quá trình thực hiện bài tập tìm kiếm các bài toán AI tiềm năng trong hệ sinh thái Vingroup, tôi sử dụng ChatGPT như một trợ lý đồng hành để hỗ trợ nghiên cứu, brainstorm ý tưởng, phân tích quy trình nghiệp vụ và đánh giá khả năng ứng dụng AI.

**1. AI đã hỗ trợ tôi như thế nào?**

Ở Phase 1 – SCAN, tôi sử dụng AI để tiếp cận hoạt động của các công ty thành viên như VinFast, Xanh SM, Vinhomes, Vinmec và Vinpearl thông qua bốn góc nhìn: công việc lặp lại, công việc tốn thời gian, cơ hội nâng cấp bằng AI và vấn đề từ phía các bên liên quan.

AI giúp tôi mở rộng phạm vi suy nghĩ, xác định các quy trình có tiềm năng tối ưu và đề xuất những bài toán cụ thể. Từ đó, tôi lựa chọn năm vấn đề ban đầu, bao gồm tự động xử lý yêu cầu bảo hành tại VinFast, tối ưu điều phối tài xế Xanh SM, phân loại phản ánh cư dân Vinhomes, hỗ trợ tiếp nhận lịch khám Vinmec và xây dựng trợ lý chăm sóc khách hàng Vinpearl.

Sang Phase 2 – QUICK-ASSESS, tôi tiếp tục sử dụng AI để lựa chọn ba bài toán tiềm năng nhất và xây dựng Quick Problem Cards. AI hỗ trợ xác định stakeholder, mô tả workflow hiện tại, tìm bước gây bottleneck, đề xuất giải pháp kỹ thuật và xây dựng các KPI định lượng.

Đặc biệt, AI giúp tôi nhận ra rằng không phải bài toán nào cũng cần sử dụng LLM. Chẳng hạn, bài toán điều phối tài xế Xanh SM phù hợp hơn với Predictive Machine Learning kết hợp thuật toán Optimization, trong khi bài toán chăm sóc khách hàng Vinpearl có thể ứng dụng LLM kết hợp RAG.

**2. Những hạn chế và sai lệch tôi phát hiện trong câu trả lời của AI**

Trong quá trình làm việc, tôi nhận thấy AI có xu hướng đưa ra những con số định lượng khá cụ thể dù chưa có dữ liệu nội bộ để chứng minh.

Ví dụ, khi phân tích bài toán tại Vinhomes, AI đề xuất mục tiêu giảm thời gian phân loại phản ánh từ 3–5 phút xuống dưới 1 phút, đạt Accuracy tối thiểu 90%. Tuy nhiên, đây chỉ là các giả định phục vụ thiết kế thử nghiệm, không phải số liệu vận hành thực tế đã được xác nhận.

Một hạn chế khác là AI đôi khi mặc định quy trình hiện tại hoàn toàn thủ công. Trên thực tế, các doanh nghiệp lớn có thể đã triển khai hệ thống quản lý, chatbot hoặc thuật toán tự động hóa. Nếu không kiểm chứng, tôi có thể đề xuất một giải pháp trùng lặp với hệ thống hiện có.

Ngoài ra, AI có xu hướng ưu tiên giải pháp AI ngay cả khi Rule-based hoặc phần mềm tự động hóa thông thường có thể xử lý vấn đề hiệu quả hơn. Ví dụ, những câu hỏi có cấu trúc cố định hoặc quy trình chuyển yêu cầu theo danh mục có thể được giải quyết bằng các quy tắc đơn giản mà không cần LLM.

**3. Tôi đã điều chỉnh prompt và thiết lập ranh giới như thế nào?**

Để khắc phục những hạn chế trên, tôi thay đổi cách đặt câu hỏi theo hướng yêu cầu AI phân tích có căn cứ thay vì chỉ đưa ra ý tưởng.

Thứ nhất, tôi yêu cầu AI phân biệt rõ thông tin công khai, giả định và thông tin cần xác minh. Điều này giúp hạn chế việc nhầm lẫn giữa số liệu ước tính và dữ liệu thực tế của doanh nghiệp.

Thứ hai, tôi yêu cầu AI đóng vai trò CFO và Trưởng phòng Vận hành để phản biện từng bài toán. AI phải chỉ ra ba điểm yếu về logic, metric và khả năng giải quyết bằng Rule-based thay vì AI. Cách tiếp cận này giúp tôi đánh giá giải pháp từ góc độ hiệu quả kinh doanh, thay vì chỉ tập trung vào công nghệ.

Thứ ba, tôi bổ sung các tiêu chí đánh giá cụ thể như tần suất xảy ra vấn đề, thời gian xử lý, chi phí, dữ liệu đầu vào, khả năng triển khai và KPI. Tôi cũng yêu cầu giải pháp phải xác định rõ phạm vi mà AI được phép hỗ trợ và những quyết định cần con người kiểm tra.

Ví dụ, đối với bài toán Vinhomes, AI chỉ nên phân loại phản ánh, tóm tắt nội dung và đề xuất hướng xử lý. Nhân viên vẫn phải kiểm tra và phê duyệt trước khi gửi phản hồi chính thức.

**4. Bài học rút ra**

Qua quá trình thực hiện, tôi nhận thấy AI là một công cụ hỗ trợ tư duy và tăng tốc quá trình nghiên cứu rất hiệu quả, nhưng không thể thay thế khả năng đánh giá và kiểm chứng của con người.

Một giải pháp AI tốt không đơn thuần là giải pháp sử dụng mô hình phức tạp nhất, mà phải giải quyết đúng bottleneck, phù hợp với dữ liệu sẵn có và tạo ra giá trị có thể đo lường.

Tôi rút ra quy trình làm việc gồm bốn bước: sử dụng AI để mở rộng ý tưởng, xác định bài toán và giả thuyết, yêu cầu AI phản biện, sau đó kiểm chứng và hoàn thiện giải pháp dựa trên bằng chứng.

Tôi xem AI là một trợ lý đồng hành, còn trách nhiệm lựa chọn vấn đề, xác định tính đúng đắn và đưa ra quyết định cuối cùng vẫn thuộc về bản thân mình.