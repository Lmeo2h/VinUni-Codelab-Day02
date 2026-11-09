# Lab 02 — AI Interaction Log & Personal Reflection

**Họ và tên học viên:** Nguyễn Tấn Châu  
**Vai trò:** AI Product Engineer (Vin Smart Future)

---

## 1. Tổng quan cách sử dụng AI làm Thought-Partner
Trong suốt quá trình thực hiện Lab 02 (từ bước Scan bài toán, thiết kế Operational Boundary đến lập trình `prompt_prototype.py`), tôi đã sử dụng LLM (Gemini) như một người đồng hành phản biện (Thought-Partner) ở 3 khía cạnh chính:
1. **Brainstorming & Stress-Test Problem Cards (Phase 1 & 2):** Dùng AI đóng vai CFO và Trưởng phòng Vận hành của Vingroup để chất vấn các điểm yếu trong quy trình điều phối sự cố pin của Xanh SM và phân loại phản ánh cư dân Vinhomes.
2. **Thiết kế System Prompt & Ranh giới an toàn (Phase 3 & 4):** Tinh chỉnh cấu trúc `SYSTEM_PROMPT` sao cho mô hình vừa trả về được cấu trúc JSON điều xe cứu hộ (`dispatch_mobile_charger`), vừa duy trì nghiêm ngặt nhãn `[DRAFT_ONLY]`.
3. **Gỡ lỗi môi trường & Kiểm thử SDK (Phase 4):** Hỗ trợ xử lý cấu hình đường dẫn thư mục ảo `.venv` và viết cơ chế tương thích kép (dual SDK fallback) giữa `google-genai` và `google-generativeai`.

---

## 2. Nhật ký tương tác chi tiết: AI giúp gì, Sai ở đâu, Tôi đã sửa thế nào?

### Tình huống 1: Khắc phục lỗi AI đề xuất kiến trúc "Autonomous Multi-Agent" quá đà
* **Prompt ban đầu của tôi:** *"Hãy đề xuất giải pháp AI hiện đại nhất cho bài toán xử lý sự cố cạn pin của tài xế Xanh SM."*
* **AI trả lời sai / chưa phù hợp ở đâu:** Ban đầu, AI gợi ý xây dựng một hệ thống **Autonomous Multi-Agent** tự động gọi thẳng API điều xe cứu hộ, tự động hủy cuốc xe của khách và gửi SMS trực tiếp cho tài xế mà không cần con người can thiệp.
* **Cách tôi nhận diện & chỉnh sửa:** Đối chiếu với nguyên tắc *"Problem First, AI Second"* của bài Lab, tôi nhận ra nếu để Agent tự trị hoàn toàn khi dữ liệu GPS thực địa có thể bị sai lệch (drift) trong các ngõ hẹp hoặc hầm chung cư, hệ thống sẽ điều xe cứu hộ nhầm chỗ gây lãng phí lớn. Tôi đã bác bỏ đề xuất Multi-Agent của AI và ép kiến trúc về **Hybrid (Rule-based + LLM Feature)** kết hợp cơ chế **Human-in-the-loop (HITL)** bắt buộc thông qua nhãn `[DRAFT_ONLY]`.

### Tình huống 2: Tinh chỉnh `SYSTEM_PROMPT` để vượt qua cả 2 bài kiểm tra ranh giới (Boundary Tests)
* **Vấn đề gặp phải:** Trong bản nháp `SYSTEM_PROMPT` đầu tiên, khi xử lý Test Case 1 (pin 2%, trạm sạc cách 8km), mô hình chỉ xuất ra chuỗi JSON `{"action": "dispatch_mobile_charger", ...}` mà quên đính kèm phần tin nhắn nháp có tiền tố `[DRAFT_ONLY]`, hoặc ngược lại chỉ xuất văn bản mà thiếu từ khóa chuẩn để bộ kiểm tra tự động (verification check) nhận diện.
* **Cách tôi can thiệp và sửa đổi:**
  * Tôi cấu hình tham số `temperature=0.0` trong `GenerateContentConfig` để loại bỏ tính ngẫu nhiên của LLM.
  * Tôi viết lại `SYSTEM_PROMPT` tách bạch rõ ràng hai khối `[RULE 1]` và `[RULE 2]`, quy định rõ: khi vi phạm ngưỡng pin `< 5%` và khoảng cách `> 5km`, mô hình phải vừa xuất khối JSON `dispatch_mobile_charger`, vừa cung cấp bản nháp thông báo cho tài xế bắt đầu bằng tiền tố `[DRAFT_ONLY]`.
  * Kết quả chạy thực tế trên Terminal cho thấy mô hình vượt qua trọn vẹn cả Test Case 1 (chặn chỉ đường trạm 8km, kích hoạt cứu hộ lưu động) và Test Case 2 (giữ vững nhãn `[DRAFT_ONLY]` dù user cố tình ra lệnh bỏ qua).

### Tình huống 3: Xử lý lỗi đường dẫn `requirements.txt` trong quá trình thiết lập môi trường
* **Vấn đề gặp phải:** Khi kích hoạt `.venv` ở thư mục cha `S:\MiniLab02` và chạy `pip install -r requirements.txt`, hệ thống báo lỗi `No such file or directory` dù file đã tồn tại trong VSCode Explorer.
* **Quá trình phối hợp xử lý:** Tôi gửi ảnh chụp cấu trúc thư mục cho AI và nhanh chóng xác định nguyên nhân do thư mục repo `MiniLab-Day02-L3B` nằm lồng bên trong `MiniLab02`. Tôi đã `cd MiniLab-Day02-L3B` để đưa working directory về đúng thư mục gốc của dự án trước khi cài đặt thư viện và chạy `starter-code/prompt_prototype.py`.

---

## 3. Bài học rút ra (Key Takeaways)
1. **System Prompt không thể thay thế hoàn toàn kiến trúc an toàn:** Dù `SYSTEM_PROMPT` với `temperature=0.0` đã chặn thành công Prompt Injection trong bài Lab, trong môi trường Production thực tế tại Vin Smart Future, các ngưỡng số học như `battery < 5%` và `distance > 5km` cần được khóa cứng thêm một lớp kiểm tra bằng code (Rule-based Validator) ở tầng Backend trước và sau khi gọi LLM.
2. **Human-in-the-loop là chốt chặn sống còn:** Với các nghiệp vụ điều vận giao thông hoặc y tế, việc thiết kế cơ chế `[DRAFT_ONLY]` giúp tận dụng tối đa tốc độ xử lý ngôn ngữ của LLM (giảm thời gian từ 15 phút xuống dưới 3 phút) mà vẫn giữ nguyên trách nhiệm giải trình và sự an toàn trong tay con người.