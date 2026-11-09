Phase 1 — SCAN

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|----------------------------------|------|---------------------|
| 1 | VinFast | Repetitive | Tự động phân loại yêu cầu bảo hành, đối chiếu lịch sử sửa chữa và xác định lỗi linh kiện từ hồ sơ kỹ thuật. |

Phase 2 — QUICK-ASSESS

```
┌─────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #___                                     │
│                                                             │
│ Bài toán (1 câu): Tự động phân loại, xác định mức độ ưu tiên|
| và đề xuất phản hồi cho các yêu cầu, khiếu nại của cư dân   |
| nhằm giảm thời gian xử lý thủ công của ban quản lý.         │
│ Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [X] Vinhomes  │
│                     [ ] Vinmec   [ ] Khác (Ghi rõ)________  │
│                                                             │
│ Ai đang đau (Actor)? Nhân viên ban quản lý tòa nhà.         |
|                      Bộ phận chăm sóc khách hàng.           |
|                      Cư dân gửi phản ánh nhưng phải chờ phản|
|                       hồi.                                  │
│                                                             │
│ Workflow thủ công hiện tại (3-5 bước):                      │
│   1.Tiếp nhận phản ánh từ ứng dụng, điện thoại hoặc email.  |
| ──> 2.Đọc nội dung và xác định loại vấn đề. ──>             | 
| 3.Đánh giá mức độ ưu tiên và xác định bộ phận phụ trách. ──>| 
| 4.Soạn phản hồi, cập nhật tiến độ và thông báo cho cư dân.  │
│                                                             │
│ Bước nào tốn thời gian/lỗi nhất? 2,3 (⏱ 5 phút/lượt)        │
│ AI có thể nhảy vào hỗ trợ ở bước nào? NLP phân loại nội dung|
| phản ánh.                                                   │
│                                                             │
│ Đo thành công bằng gì (Metric có số)? Thời gian phân loại,  | 
| Accuracy phân loại                                          │        
│   VD: "Giảm thời gian soạn phản hồi từ 10 min ──> under 2 min"│
│                                                             │
│ Quick Architecture: [ ] No AI  [X] Rule  [X] LLM  [X] Agent │
└─────────────────────────────────────────────────────────────┘
```
