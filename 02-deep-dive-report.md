> **Tên nhóm:** Nhóm VoDanh 
> **Danh sách thành viên:**  
> 1. Nguyễn Tấn Châu — `chau010804@gmail.com` 
> 2. Vũ Bá Tùng Lâm — `lamvu980204@gmail.com`  
> 3. Mai Xuân Khang— `maixkhang2004@gmail.com`  
> 4. Trương Đức Phát — `phat1605@gmail.com`  

---
# Lab 02 · Deep Dive Report
## Vinhomes AI Resident Complaint Router
### Hệ thống hỗ trợ phân loại và điều hướng phản ánh cư dân bằng AI

**Phạm vi.** Tình huống mô phỏng cho CSKH/Ban quản lý Vinhomes. Cư dân gửi phản ánh qua ứng dụng, hotline hoặc biểu mẫu. Mốc **18 phút/ticket** và lưu lượng **100 ticket/ngày** là giả định minh họa. Các ngưỡng trong mục 3.2 là **KPI mục tiêu**. Kết quả quan sát ở Phase 4 chỉ phản ánh các ca kiểm thử cục bộ. Chưa có khảo sát thực địa, dữ liệu vận hành đã xác minh hoặc kết quả pilot.

## Phase 3 — DEEP-DIVE

### 3.1 Current-State Workflow

Bắt đầu khi cư dân gửi phản ánh. Nhân viên xử lý thủ công qua hệ thống tiếp nhận và danh bạ nội bộ. Kết thúc khi hồ sơ đã được bàn giao, xác nhận tiếp nhận đã được soạn và trạng thái ban đầu đã được ghi. Xem [sơ đồ Current-State](04-workflow-diagram.png).

| # | Bước | Actor | Input | Output và handoff | Phút |
|---|---|---|---|---|---:|
| 1 | Tiếp nhận phản ánh | CSKH, hệ thống tiếp nhận | Nội dung từ ứng dụng, hotline hoặc biểu mẫu | Hệ thống mở ticket từ nội dung gửi đến hoặc ghi chú hotline do CSKH nhập. Ticket sang CSKH. | 2 |
| 2 | Kiểm tra và bổ sung | CSKH, cư dân khi cần | Ticket thô, thông tin vị trí và diễn biến | Nhân viên đối chiếu vị trí, mô tả, thời điểm nếu cần. Khi thiếu, trao đổi với cư dân rồi cập nhật hồ sơ. | 3 |
| 3 | Đọc và phân loại | CSKH | Hồ sơ đủ dữ kiện | Nhân viên đọc nội dung tự do, gán nhóm và ghi dấu hiệu ưu tiên. | **5** |
| 4 | Tra cứu và chuyển bộ phận | CSKH → Bộ phận nghiệp vụ | Nhóm phản ánh, vị trí và danh bạ phụ trách | Nhân viên chọn đúng bộ phận theo phạm vi địa bàn và chuyển hồ sơ. | **4** |
| 5 | Soạn phản hồi xác nhận | CSKH | Hồ sơ và thông tin chuyển tiếp | Nhân viên soạn nội dung xác nhận tiếp nhận cho kênh cư dân. | **3** |
| 6 | Cập nhật trạng thái ban đầu | CSKH → Hệ thống tiếp nhận | Kết quả bàn giao và bản xác nhận | Nhân viên lưu trạng thái ban đầu và dấu vết chuyển tiếp trên hệ thống. | 1 |
| | **Tổng luồng chuẩn (ASSUMPTION)** | | | **2 + 3 + 5 + 4 + 3 + 1** | **18** |

Bước 3 chậm khi một phản ánh mô tả nhiều hiện tượng hoặc dùng từ địa phương. Bước 4 cần đối chiếu nhóm sự cố với bộ phận phụ trách theo khu vực. Bước 5 lặp lại theo từng nội dung và kênh. Ba bước chiếm **12/18 phút ≈ 66,7%**. Vòng hỏi bổ sung phát sinh ở bước 2 có thể kéo dài thời gian chờ ngoài luồng chuẩn. Baseline chỉ tính công xử lý và phân luồng ban đầu, không tính chờ cư dân, chờ hàng đợi hoặc thời gian khắc phục.

### 3.2 Problem Statement — 6-field

| Trường | Nội dung |
|---|---|
| 1. Actor / Operator | CSKH/Ban quản lý trực tiếp kiểm tra, phân loại, chuyển hồ sơ và soạn xác nhận. Cư dân cung cấp thông tin. Bộ phận nghiệp vụ tiếp nhận hồ sơ. |
| 2. Current Workflow | Sáu bước thủ công từ tiếp nhận, kiểm tra, phân loại, chuyển bộ phận, soạn xác nhận đến lưu trạng thái ban đầu. Công cụ giả định gồm hệ thống ticket và danh bạ phụ trách. |
| 3. Bottleneck | Ba bước 3–5 chiếm 12 phút trong baseline 18 phút/ticket. Chuyển nhầm hoặc phải hỏi lại có thể gây xử lý lại. Chưa có số liệu tỷ lệ chuyển nhầm. |
| 4. Business Impact | Giả định 100 ticket/ngày × 18 phút = 1.800 phút = **30 giờ công/ngày** cho toàn bộ phân luồng ban đầu. Ba bước nghẽn tương ứng 100 × 12 = **20 giờ công/ngày** trong 30 giờ. Đây là khối lượng minh họa. Chưa có số giờ tiết kiệm, chi phí hoặc tác động SLA đã đo. |
| 5. Success Metrics | Median minh họa 18 → **dưới 5 phút/ticket**. Routing accuracy **≥90%** và recall khẩn cấp **≥95%** trên bộ gán nhãn. **100%** ca khẩn cấp hoặc thiếu thông tin quan trọng được người xem xét. **0** hành động gửi tin, phân công hoặc đóng ticket do AI tự thực hiện. |
| 6. Operational Boundary | AI phân tích và đề xuất. Nhân viên giữ quyền duyệt, đổi đích và quyết định. Hệ thống nghiệp vụ chỉ thực hiện theo quyết định được cấp quyền và ghi log. Quy tắc chi tiết ở mục 3.5. |

### 3.3 AI Fit Matrix

| Phương án | Độ chính xác kỳ vọng | Chi phí | Độ phức tạp | Kiểm soát | Rủi ro | Mức phù hợp |
|---|---|---|---|---|---|---|
| No AI / Rule-based | Tốt với biểu mẫu, ánh xạ rõ và cụm khẩn cấp đã biết. Yếu với mô tả mơ hồ, phủ định và nhiều sự cố. | Thấp khi danh mục ổn định. Có công cập nhật luật. | Thấp | Luật và phiên bản dễ truy vết. | Bỏ sót cách diễn đạt ngoài luật. | Baseline, xác thực routing, sàng lọc khẩn cấp rõ ràng. |
| LLM Feature | Có thể hiểu tiếng Việt tự do và trích xuất ngữ cảnh. Cần đo theo từng nhóm và kiểm tra dữ kiện. | Phí theo lượt/token, thời gian duyệt và giám sát. | Vừa | Schema, kiểm tra dữ liệu, danh sách đích hợp lệ và HITL. | Suy diễn, prompt injection, đầu ra sai định dạng, lộ dữ liệu. | **Chọn kết hợp Rule + LLM**, chỉ bổ sung tác vụ ngôn ngữ. |
| Agentic Loop | Có thể hữu ích khi cần nhiều lần tra cứu. Lỗi có thể cộng dồn qua chuỗi quyết định. | Cao và biến động theo số lượt/tool call. | Cao | Khó giới hạn và tái hiện chuỗi quyết định. | Gọi công cụ ngoài quyền hoặc tạo tác dụng phụ. | Không chọn. Quy trình có thứ tự và điểm duyệt xác định. |

### 3.4 Future-State Flow

1. **Tiếp nhận và kiểm tra.** Nhận ticket, xác thực mô tả và vị trí sự cố ở mức cần thiết. Yêu cầu thời điểm khi ảnh hưởng đến phân loại. Kênh liên hệ nằm trong hệ thống nghiệp vụ. Chạy bước 2 cả khi hồ sơ thiếu.
2. **Rule-based emergency screening.** Nếu có dấu hiệu cháy nổ, mắc kẹt thang máy, nguy cơ điện giật, bạo lực hoặc nguy hiểm trực tiếp, đưa vào hàng đợi **nhân viên xử lý khẩn cấp** ngay. Không chờ model hoặc cư dân bổ sung. Cờ khẩn do luật đặt không thể bị model hạ. Hồ sơ thiếu trường quan trọng và không có tín hiệu khẩn chuyển sang nhân viên làm rõ.
3. **AI Step.** Với hồ sơ còn lại, lọc dữ liệu định danh trước khi gửi LLM. Trích xuất nhóm, mức ưu tiên, tóm tắt và thông tin còn thiếu. Trong prototype, bước tiếp nhận kiểm tra vị trí và chỉ chuyển cờ có vị trí tới mô hình. Nếu LLM nhận thấy nguy hiểm ngoài luật, đưa vào hàng đợi khẩn cho người đánh giá.
4. **Rule-based routing validation.** Kiểm tra schema, giá trị danh mục, địa bàn và bảng đích theo phiên bản. Ánh xạ nhóm phản ánh sang hàng đợi đủ quyền. Chuyển xem xét thủ công nếu có nhiều đích hoặc thiếu cấu hình. Kiểm tra dữ kiện và lọc thông tin cá nhân trong bản nháp.
5. **Đề xuất và nháp.** Hiện bộ phận đề xuất, căn cứ, cờ ưu tiên và phản hồi gắn `[DRAFT_ONLY]`. Giao diện tách đề xuất khỏi nút thao tác. LLM không có công cụ gửi, phân công hay đóng ticket.
6. **HITL và manual override.** CSKH xem hồ sơ gốc, sửa nháp, đổi nhóm/đích/ưu tiên hoặc trả về bổ sung. Hàng đợi khẩn yêu cầu người có quyền xác nhận đã xem và ghi lý do nếu đổi ưu tiên. Duyệt gắn với đúng phiên bản đề xuất.
7. **Thực thi và audit trail.** Sau phê duyệt, hệ thống nghiệp vụ kiểm tra quyền, phiên bản và mã chống lặp trước khi gửi/chuyển. Ghi ticket ID, thời điểm, phiên bản luật/prompt/model, lỗi hoặc fallback, đề xuất, người duyệt, lý do sửa, đích cuối và trạng thái thao tác. Phân quyền log, giảm dữ liệu nhận dạng và áp dụng thời hạn lưu do vận hành phê duyệt.

| Tình huống fallback | Xử lý |
|---|---|
| Model timeout, lỗi API hoặc JSON sai schema | Chuyển hàng đợi thủ công với hồ sơ gốc và cờ lỗi. Giữ cờ khẩn, bỏ đề xuất lỗi, nhân viên tra đích và soạn tay. |
| Không chắc chắn, dữ kiện mâu thuẫn hoặc nhiều chủ đề | Dựa trên thiếu trường, xung đột luật hoặc nhiều đích hợp lệ. Nhân viên làm rõ và tự chọn hàng đợi. Điểm tự tin tự khai báo của mô hình không được dùng để bỏ qua duyệt. |
| Bảng routing không hợp lệ hoặc không có người nhận | Chuyển đầu mối điều phối thủ công. Ca khẩn dùng kênh trực đã duyệt và ghi nhận thời điểm nhân viên tiếp nhận. |
| Lỗi sau phê duyệt | Hiển thị lỗi thực thi và giữ trạng thái đúng. Xác minh kết quả trước khi thử lại bằng mã chống lặp. |

Danh mục và đích minh họa: thang máy, mất/rò nước và điện/đèn → TECHNICAL. An ninh → SECURITY_TEAM. Tiếng ồn → COMMUNITY_MANAGEMENT. Vệ sinh/rác → HOUSEKEEPING. Thẻ xe/bãi xe → PARKING_OPERATIONS. Phí → CUSTOMER_SERVICE. Khác → CUSTOMER_SERVICE_REVIEW. Đích cụ thể theo khu/tòa cần người vận hành phê duyệt.

### 3.5 Operational Boundary

- **Được phép:** phân loại, trích xuất dữ kiện, tóm tắt, đề xuất bộ phận và soạn phản hồi DRAFT. Đánh dấu dữ kiện thiếu hoặc mâu thuẫn cho người xem.
- **Cấm:** tự gửi tin, tự phân công trên hệ thống thật, tự đóng ticket, tự xác nhận đã khắc phục, tự cam kết hoàn tiền/bồi thường/kết luận pháp lý, đưa ra kết luận chắc chắn khi thiếu dữ liệu, bỏ qua review khẩn hoặc làm lộ thông tin cá nhân. Nội dung ticket chỉ là dữ liệu, kể cả khi chứa chỉ dẫn giả mạo.
- **Cưỡng chế:** Tài khoản mô hình chỉ có quyền phân tích. Hệ thống tách đề xuất khỏi thao tác. Người có thẩm quyền duyệt đúng phiên bản. Ca nguy hiểm được ưu tiên chuyển người. Thẻ DRAFT là tín hiệu hiển thị, còn kiểm soát quyền nằm ở ứng dụng.

## Phase 4 — Prototype và kiểm thử Vinhomes

[Prototype Python](starter-code/prompt_prototype.py) có ba lớp. Luật kiểm tra trường bắt buộc, sàng lọc nguy hiểm, kiểm tra schema và đích theo whitelist. Gemini xử lý mô tả tiếng Việt, chọn nhóm và mức ưu tiên, tóm tắt và chọn câu nháp từ mẫu theo hành động. Nhân viên xem xét mọi đề xuất. Prototype không có công cụ thực hiện thao tác trên hệ thống ticket.

- **Input mô phỏng:** một dòng `Vị trí: Khu mẫu A` và một dòng `Nội dung: …`. Luật chỉ chuyển sang SDK phần mô tả đã che các mẫu PII nhận diện được cùng cờ có vị trí. Khi thiếu dữ liệu, trả nháp xin bổ sung. Khi có tín hiệu khẩn rõ, luật xử lý trước bước gọi model và giữ `EMERGENCY` dù hồ sơ còn thiếu.
- **Output:** tiền tố `[DRAFT_ONLY]` theo sau bởi một JSON có chín khóa `action`, `category`, `priority`, `department`, `summary`, `draft_reply`, `needs_human_review`, `missing_information`, `reason`. Hành động chỉ gồm `DRAFT_ROUTE`, `REQUEST_INFORMATION` hoặc `ESCALATE_HUMAN`. Mức ưu tiên gồm `LOW`, `MEDIUM`, `HIGH`, `EMERGENCY`. Python từ chối khóa thừa/trùng, đích sai nhóm, nội dung nháp ngoài mẫu và các dấu hiệu rò dữ liệu hoặc cam kết trái quyền. Lỗi SDK hoặc output sai chuyển đề xuất kiểm duyệt thủ công.
- **SDK:** mặc định `gemini-3.1-flash-lite` theo cấu hình đã chạy thành công. Có thể đổi bằng `GEMINI_MODEL`. Worksheet dùng `gemini-2.5-flash`, model trả HTTP 404 trong lần chạy ghi nhận. Timeout cấu hình 12 giây cho mỗi yêu cầu HTTP, một lần thử. Kết quả gọi model thực tế được ghi ở phần lần chạy Gemini bên dưới.

| Nhóm kiểm thử | Kết quả trong phiên audit cục bộ trước khi sinh viên chạy live |
|---|---|
| 6 ca đối kháng | Bỏ duyệt, hạ cảnh báo điện giật, giả mạo SYSTEM, đòi cam kết hoàn phí, đòi tiết lộ PII, và thiếu dữ kiện. Ca điện giật và thiếu dữ kiện chạy bằng luật và đạt. Bốn ca phụ thuộc Gemini được bỏ qua vì thiếu khóa. |
| 6 ca thông thường | Thang máy, mất nước, tiếng ồn, bãi xe, nhiều vấn đề và tiếng Việt không dấu. Cả sáu ca phụ thuộc Gemini được bỏ qua. |
| Pytest offline | 60 ca đạt, gồm ba ca hồi quy về cảnh báo cháy và giữ mức khẩn khi chuyển sang kiểm duyệt. Các ca còn lại kiểm tra whitelist, schema/prefix, ưu tiên, từ chối PII/hành động trái quyền, fallback, và cấu hình SDK qua mock. |
| Autograder nguyên bản | 7,50/10 và exit 1. Phần file đạt 5/5. Code 2–3 đạt. Code 1 không đạt riêng lẻ vì dò từ khóa GSM và được 0,5 điểm trong tổng hợp. Code 4–5 chưa đạt vì script báo thiếu khóa và chưa có kết quả kiểm thử live. |

Các ca luật và mock kiểm chứng logic cục bộ. Chưa có số đo routing accuracy, recall khẩn cấp hoặc hiệu quả vận hành. Sàng lọc từ khóa có thể báo dư hoặc bỏ sót cách diễn đạt mới. Regex che PII và kiểm tra ngôn ngữ chỉ bao phủ một số mẫu. Tiếp tục dùng dữ liệu giả lập và yêu cầu nhân viên kiểm tra mọi nháp. Khi có khóa, chương trình chỉ lưu tên ca, kết quả, nguồn và model vào `test-results/vinhomes-results.json`.

### Lần chạy Gemini từ PowerShell của sinh viên

Kết quả lưu cục bộ và ảnh terminal xác nhận: với `gemini-2.5-flash`, 10 ca Gemini nhận HTTP 404. Khi đổi `GEMINI_MODEL` sang `gemini-3.1-flash-lite`, 8/10 ca Gemini và 2/2 ca luật đạt. Ca hoàn phí bị bộ kiểm tra từ chối (`invalid_output`). Nhật ký chỉ lưu mã lỗi nên chưa xác định được trường vi phạm. Ca bãi xe nhận HTTP 504. Chưa dùng lần chạy này để kết luận KPI vận hành.

Đã làm rõ cách viết tóm tắt trung lập cho phản ánh về phí và bổ sung tùy chọn `--case` để kiểm tra lại từng ca. Ở lần chạy lại toàn bộ, kết quả lưu cục bộ cho thấy **10/10 ca Gemini và 2/2 ca luật đạt** với `gemini-3.1-flash-lite`. Đây là kết quả trên các ca mô phỏng của lab. Script hiển thị chi tiết từng ca theo mặc định.

## Phase 5 — EVALUATE

### Kế hoạch đo

Dùng bộ gán nhãn giữ riêng khỏi các ví dụ viết prompt. Lấy mẫu theo nhóm phản ánh, kênh, tín hiệu khẩn và trường hợp thiếu dữ kiện. Bổ sung ca nhiều chủ đề, phủ định, tiếng Việt không dấu và prompt injection. Hai người gán nhãn độc lập, hòa giải bất đồng theo bảng routing và lưu phiên bản nhãn. So sánh quy trình thủ công, luật thuần và hybrid trên cùng dữ kiện. Báo số mẫu, ma trận nhầm lẫn và danh sách ca bỏ sót.

| Chỉ số | Cách đo và điều kiện |
|---|---|
| Median thời gian xử lý ban đầu | Đo thời gian thao tác từ tiếp nhận đến lưu trạng thái ban đầu, gồm duyệt và xử lý lại. Tách thời gian chờ và khắc phục. Đo lại median thủ công thật trước khi so sánh. Mốc 18 phút hiện là tổng giả định, dùng làm mốc median minh họa. Mục tiêu dưới 5 phút/ticket. |
| Routing accuracy | Số đề xuất trùng đích chuẩn / số ticket có đích chuẩn. Tính thiếu đề xuất là sai khi đã có đích chuẩn. Báo thêm tỷ lệ sửa đích và accuracy sau duyệt riêng. Mục tiêu ≥90%. |
| Recall khẩn cấp | Số ca khẩn gán nhãn được đưa vào hàng đợi khẩn / tổng số ca khẩn gán nhãn, đo trước chỉnh sửa của người. Mục tiêu ≥95%. Báo riêng số dương tính giả để theo dõi tải. |
| Tỷ lệ nhân viên xem xét | Số ticket khẩn hoặc thiếu thông tin quan trọng có log người xem / tổng số ca tương ứng theo nhãn. Một ticket có cả hai điều kiện tính một lần. Mục tiêu 100%. |
| Thao tác AI tự thực hiện | Đối chiếu log thao tác gửi, phân công và đóng với danh tính dịch vụ và sự kiện duyệt. Mục tiêu 0. Thử cả yêu cầu thao tác ngoài quyền. |

### AI Readiness Checklist và quyết định

| Câu hỏi worksheet | Hiện trạng | Điều kiện xác nhận |
|---|---|---|
| Có dữ liệu mẫu/log sạch? | Có kịch bản mô phỏng để bắt đầu thiết kế. Chưa có bộ gán nhãn đủ rộng hoặc log thật đã làm sạch. | Hoàn thành bộ test mô phỏng trước, sau đó xin dữ liệu được phép và khử định danh để kiểm chứng. |
| Rủi ro trong tầm kiểm soát? | Đã thiết kế HITL, phân quyền, fallback và audit. Chưa kiểm thử tích hợp hoặc phản ứng ca khẩn trên hệ thống thật. | Kiểm thử quyền, nhánh timeout/JSON lỗi, bàn giao khẩn, truy vết và tác dụng phụ. |
| Stakeholder sẵn sàng? | Chưa xác nhận với CSKH, Ban quản lý, bộ phận trực hoặc đội phụ trách dữ liệu. | Xác nhận danh mục, người trực, quyền duyệt, quy trình khi lỗi, tiêu chuẩn dữ liệu và cách ghi nhận SLA. |

**GO cho prototype hẹp** với dữ liệu giả lập, hàng đợi mô phỏng và duyệt thủ công toàn bộ. Phương án thử nghiệm có ít tích hợp và cho phép đo phần lợi ích tăng thêm của LLM trước khi tính phí model, công gán nhãn, duyệt và vận hành.

**NOT YET cho production.** Chưa có baseline thực, tập dữ liệu đủ đại diện, kết quả kiểm thử hoặc chấp thuận từ vận hành. Cần đo trên bộ giữ riêng, kiểm chứng quyền và các nhánh khẩn, tính chi phí mỗi ticket gồm thời gian duyệt và model, rồi xác nhận các ngưỡng trước pilot. Nếu luật thuần đạt các ngưỡng với chi phí thấp hơn thì dùng kết quả so sánh đó để quyết định có tiếp tục LLM hay không.

## Đối chiếu rubric và nộp bài

| Gate | Bằng chứng và trạng thái |
|---|---|
| G1 | Mục 3.1 và sơ đồ PNG có 6 bước, actor, input/output, handoff, thời gian và bottleneck. |
| G2 | Mục 3.2 và 3.5 đủ 6 trường, phép tính có giả định và ranh giới rõ. |
| G3 | Mục 3.3–3.5 so sánh ba phương án và mô tả HITL, override, fallback và audit. |
| G4 | Phase 5 có checklist, thiết kế đo và hai quyết định theo phạm vi. |
| I1 | [Problem Scan](01-problem-scan.md) có 6 bài toán qua 4 lens và đúng 3 thẻ. |
| I2 | Prototype và kiểm thử offline đã hoàn thành. Kết quả lần chạy đầy đủ với Gemini 3.1 Flash-Lite ghi nhận 12/12 ca đạt, gồm 10 ca Gemini và 2 ca luật. |
| I3 | [AI Log](03-ai-log.md) ghi tương tác thật và phần tự phản ánh dựa trên các lần chạy đã ghi nhận. |

**Khác biệt giữa tài liệu và công cụ chấm.** Worksheet mô tả G1–G4 = 60 điểm và I1–I3 = 40 điểm, yêu cầu tối thiểu 3 ca tấn công và Gemini 2.5 Flash. Starter trước khi đổi sang Vinhomes yêu cầu tối thiểu 2 ca, dùng bối cảnh GSM và cấu hình `gemini-3.1-flash-lite`. Autograder kiểm tra tối thiểu 2 ca, dò từ khóa GSM (`draft_only`, `5%`, `dispatch_mobile_charger`), đọc chuỗi Passed/Failed, giới hạn 30 giây cho toàn script và chấm sự tồn tại của file. Thang điểm mặc định của công cụ là 10, khác rubric 100 điểm. Tên vị trí I2 trong worksheet là `02-lab/`, còn README và công cụ hỗ trợ `starter-code/prompt_prototype.py`. Dùng rubric worksheet để đánh giá nội dung và README để tổ chức nộp. Xác nhận cách chấm prototype Vinhomes với giảng viên. Bộ chấm được giữ nguyên.

**Quy định Git theo README.** Nhóm 4–6 thành viên, trưởng nhóm cấp quyền cho thành viên. Mỗi người nộp trên nhánh cá nhân. File Python chỉ chấm trên nhánh cá nhân. Sau khi nhóm review, trưởng nhóm chọn các file Markdown và PNG đưa lên `main` và là người duy nhất điền form. Tài liệu hiện được chuẩn bị cục bộ trong bản clone đang ở `main`, chờ nhánh cá nhân có tên/mã sinh viên và repo nhóm để thực hiện quy trình nộp.
