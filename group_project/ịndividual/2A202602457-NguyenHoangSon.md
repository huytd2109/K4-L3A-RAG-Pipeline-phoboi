# Individual contribution report

## Thông tin

- Họ và tên: Nguyễn Hoàng Sơn
- Mã học viên: 2A202602457
- Nhóm: phoboi
- Repository/branch: `huytd2109/K4-L3A-RAG-Pipeline-phoboi` / `sonnn`

## Phần việc đã thực hiện

| Module/deliverable | Việc tôi trực tiếp làm | File/commit/PR | Trạng thái |
|---|---|---|---|
| Task 10 — Generation có citation | Hoàn thiện hàm reorder chunks, giữ nguyên ID và không làm thay đổi danh sách đầu vào | `src/task10_generation.py`, commit `d77552d`, PR #3 | Done / Merged |
| Xây dựng context cho LLM | Định dạng từng chunk với số document, title, source và nội dung để câu trả lời có thể trích dẫn nguồn | `src/task10_generation.py`, commit `d77552d` | Done / Merged |
| Multi-provider generation | Dispatch theo `LLM_PROVIDER`, hỗ trợ OpenAI, Gemini và Anthropic với cùng interface trả về text | `src/task10_generation.py`, commit `d77552d` | Done / Merged |
| End-to-end generation | Nối retrieval → reorder → format context → gọi LLM → trả `GenerationResult`; trả safe refusal khi không có context hoặc provider lỗi | `src/task10_generation.py`, commit `d77552d`, merge commit `f6ab3fa` | Done / Merged |

## Quyết định kỹ thuật quan trọng

1. **Quyết định:** Sắp xếp xen kẽ các chunk quan trọng về đầu và cuối context nhưng không sửa trực tiếp danh sách retrieval.  
   **Lý do/evidence:** Giảm ảnh hưởng “lost in the middle”, đồng thời giữ nguyên ID và danh sách `sources` để citation có thể đối chiếu. Contract test xác nhận input không bị thay đổi và không mất chunk.  
   **Trade-off:** Thứ tự context không còn hoàn toàn giống thứ tự điểm retrieval, nên cần nhãn document rõ ràng để truy vết nguồn.

2. **Quyết định:** Chuẩn hóa ba nhà cung cấp LLM qua một hàm `call_llm()` và dùng safe refusal khi thiếu bằng chứng hoặc provider gặp lỗi.  
   **Lý do/evidence:** Phần còn lại của pipeline không phụ thuộc SDK cụ thể; lỗi API không làm chatbot crash hoặc tạo câu trả lời không có căn cứ.  
   **Trade-off:** Bắt lỗi provider và trả refusal giúp hệ thống an toàn nhưng có thể che khuất nguyên nhân kỹ thuật nếu chưa bổ sung logging/monitoring.

## Kiểm thử và kết quả

- Test đã dùng: `python -m pytest tests/test_contracts.py -q`.
- Kết quả ghi nhận: `15 passed` trong `29.81s`.
- Các contract liên quan đã được kiểm tra: chữ ký public của `generate_with_citation`, reorder không mutate/mất ID, context chứa title/source và safe refusal đúng schema `GenerationResult`.
- Lỗi đã xử lý: trường hợp retrieval không trả chunk và trường hợp LLM provider phát sinh exception đều trả câu từ chối an toàn với `sources=[]` và `retrieval_source="none"`.

## Điều còn hạn chế

- Citation hiện chủ yếu được ràng buộc bằng prompt; phần đóng góp ban đầu chưa có bước hậu kiểm tự động để xác nhận mọi citation trong answer đều tồn tại trong `sources`.
- Nếu có thêm thời gian, thay đổi đầu tiên tôi sẽ thực hiện là bổ sung unit test mock cho cả ba provider và bộ kiểm tra citation-to-source trước khi trả kết quả ra UI.

## Xác nhận đóng góp

Tôi xác nhận nội dung trên phản ánh đúng phần việc của mình và có thể giải thích hoặc chạy lại trong buổi demo.

- Ngày: 20/09/2026
- Tên thành viên: Nguyễn Hoàng Sơn
