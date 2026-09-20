# Individual contribution report

## Thông tin

- Họ và tên: Trịnh Đức Huy
- Mã học viên: 2A202602865
- Nhóm: phoboi
- Repository: https://github.com/huytd2109/K4-L3A-RAG-Pipeline-phoboi
- GitHub/branch: `huytd2109` / `huytd2109`

## Phần việc đã thực hiện

| Module/deliverable | Việc tôi trực tiếp làm | File/commit/PR | Trạng thái |
|---|---|---|---|
| Task 1–3 — thu thập và chuẩn hóa dữ liệu | Xây dựng kiểm tra/tải tài liệu pháp lý; crawler 5 bài Vietnam Tourism có bước loại menu, footer và nội dung rác; chuyển PDF/DOCX/JSON sang Markdown có metadata và tránh tạo lại file không đổi. | `src/task1_collect_legal_docs.py`, `src/task2_crawl_news.py`, `src/task3_convert_markdown.py`; commits `7e4512e`, `16c3df3`, `3e55045`; PR #1 | Done — đã merge vào `main` |
| Task 4–7 — indexing và hybrid retrieval | Triển khai recursive chunking, embedding dùng chung cho index/query, Chroma cosine, dense search, BM25Plus và Reciprocal Rank Fusion; chuẩn hóa kết quả theo `SearchResult`. | `src/task4_chunking_indexing.py`, `src/task5_semantic_search.py`, `src/task6_lexical_search.py`, `src/task7_reranking.py`; commit `4bc3850`; PR #1 | Done — đã merge vào `main` |
| Golden dataset và báo cáo đánh giá | Tạo 15 câu hỏi có đáp án, gold span và nguồn (6 legal, 9 news); hoàn thiện báo cáo A/B dense-only so với hybrid + RRF, nêu rõ cách tính metric, latency, lỗi điển hình và khuyến nghị. | `group_project/evaluation/golden_dataset.json`, `group_project/evaluation/RESULT.md`; commits `e3f9192`, `ddcdf71` | Partial — dataset/report đã có; evaluation scripts và raw result chưa được track |

## Quyết định kỹ thuật quan trọng

1. **Quyết định:** Kết hợp dense retrieval và BM25Plus bằng RRF thay vì cộng trực tiếp hai loại score.

   **Lý do/evidence:** Cosine similarity và BM25 không cùng thang đo; RRF chỉ dùng thứ hạng. Trên 15 câu đánh giá, hybrid lấy đúng gold span 13/15, cao hơn dense-only 12/15 và khắc phục case `legal-03`.

   **Trade-off:** Pipeline có thêm bước BM25/RRF; latency retrieval trung bình của lần đo tăng từ 235,53 ms lên 239,39 ms. Kết quả mới là một lần đo trên tập nhỏ nên chưa đủ để kết luận tổng quát.

2. **Quyết định:** Dùng recursive chunking 500 ký tự, overlap 50, ID chunk ổn định và cùng cấu hình embedding cho index/query.

   **Lý do/evidence:** Giữ pipeline chạy lại không nhân bản dữ liệu, bảo toàn metadata/source và đáp ứng contract giữa Task 4–7; lần đánh giá dùng 534 chunks với `BAAI/bge-m3`, cosine.

   **Trade-off:** Kích thước cố định có thể tách điều khoản pháp lý khỏi tiêu đề/ngữ cảnh; các case `legal-01` và `legal-03` cho thấy cần thử chunk theo điều/mục.

## Kiểm thử và kết quả

- Test/lệnh đánh giá đã dùng:
  - `.venv\Scripts\python.exe -m pytest -q`
  - `.venv\Scripts\python.exe -m group_project.evaluation.evaluate_retrieval`
  - `.venv\Scripts\python.exe -m group_project.evaluation.evaluate_generation`
- Theo `group_project/evaluation/RESULT.md` tại commit `ddcdf71`: `20 passed`; 15/15 mẫu generation và 15/15 mẫu judge cho mỗi cấu hình hoàn tất, không có provider error cuối cùng. Repository hiện xác minh trực tiếp được golden dataset có 15 ID duy nhất và đủ `question`, `expected_answer`, `expected_context`, `source`.
- So sánh A/B: macro-average bốn metric tăng từ `0,7150` lên `0,7433`; context recall proxy tăng `0,8000 → 0,8667`; faithfulness tăng `0,9667 → 1,0000`; answer relevance giữ nguyên `0,9333`.
- Lỗi đã phát hiện và cách xử lý: `legal-02` có evidence tương đương nhưng exact-span proxy vẫn báo miss, nên báo cáo không đồng nhất proxy này với chất lượng ngữ nghĩa và đề xuất bổ sung nhãn relevant tương đương. Với `legal-01`/`legal-03`, tôi xác định lỗi ở retrieval/chunking và đề xuất chunk theo điều/mục, gắn tên/mã văn bản vào chunk.

## Điều còn hạn chế

- Tập đánh giá mới có 15 câu in-domain; context recall/precision là exact-span proxy, còn faithfulness/relevance do cùng model Gemini chấm một lần nên có nguy cơ self-evaluation bias. Fallback threshold `0.3` chưa được hiệu chỉnh trong A/B này. Hai script evaluation và raw result được `RESULT.md` nhắc tới chưa được track trên nhánh hiện tại nên chưa thể tái lập đầy đủ chỉ từ repository.
- Nếu có thêm thời gian, thay đổi đầu tiên tôi sẽ thực hiện: đưa evaluation harness và raw artifacts vào repository; sau đó mở rộng nhãn relevant theo ngữ nghĩa/câu hỏi out-of-domain và A/B chunk theo điều/mục với evaluator độc lập hoặc RAGAS.

## Xác nhận đóng góp

Tôi xác nhận nội dung trên phản ánh đúng phần việc của mình và có thể giải thích hoặc chạy lại trong buổi demo.

- Ngày: 20/09/2026
- Tên thành viên: Trịnh Đức Huy
