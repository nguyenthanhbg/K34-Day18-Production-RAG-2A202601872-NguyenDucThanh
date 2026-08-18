## Reflection cá nhân - Lab 18

### Phần 1: Mapping bài giảng
| Lecture Concept | Module | Hàm cụ thể | Observation |
|----------------|--------|-------------|-------------|
| Semantic chunking | M1 | `chunk_semantic()` | Tách chunk hiệu quả, không bị đứt đoạn giữa câu, giúp bảo toàn ngữ nghĩa tốt hơn chunk basic. |
| Hierarchical chunking | M1 | `chunk_hierarchical()` | Cung cấp ngữ cảnh rộng cho child chunk, tăng precision cho truy xuất. |
| Structure chunking | M1 | `chunk_structure_aware()` | Duy trì cấu trúc tài liệu, rất phù hợp với file Markdown có nhiều Heading. |
| BM25 + Dense fusion | M2 | `reciprocal_rank_fusion()` | Kết hợp ưu điểm keyword của BM25 và ngữ nghĩa của vector, nâng cao recall. |
| Cross-encoder reranking | M3 | `CrossEncoderReranker.rerank()` | Tăng độ chính xác (precision) bằng cách chấm điểm lại tương quan giữa query và doc. |
| RAGAS 4 metrics | M4 | `evaluate_ragas()` | Giúp thấy rõ điểm mạnh yếu: Context Precision cải thiện rõ rệt nhờ reranking. |
| Contextual embeddings | M5 | `_enrich_single_call()` | Giảm thiểu retrieval failure nhờ việc tự động hóa tóm tắt, tạo HyQA và trích xuất Metadata. |

### Phần 2: Khó khăn & giải quyết
- **Lỗi gặp phải:** Lỗi `UnicodeEncodeError` khi in các icon `⚠️` ra console Windows (`python src/pipeline.py`).
- **Cách debug & giải quyết:** Thiết lập biến môi trường `$env:PYTHONIOENCODING="utf-8"` để ép PowerShell sử dụng chuẩn UTF-8 khi xuất log ra console.
- **Vấn đề cài đặt thư viện:** Cài `numpy` hoặc `ragas` trên phiên bản Python 3.13 bị lỗi build do thiếu compiler C++. Đã xử lý bằng cách nới lỏng version bounds trong `requirements.txt` để pip tìm được wheel build sẵn tương thích.

### Phần 3: Action Plan cho project
## Project: Hệ thống tra cứu văn bản
### Hiện tại
- RAG pipeline hiện tại: Đang sử dụng chunking cơ bản theo kích thước (size-based) và tìm kiếm Vector truyền thống, dẫn đến kết quả trả về đôi khi bị lệch context hoặc thiếu thông tin.
- Known issues: Chưa xử lý tốt các câu hỏi yêu cầu từ khóa chính xác, và chunk thường bị cắt giữa chừng ý.

### Plan áp dụng
1. [x] Chunking strategy: Áp dụng Semantic Chunking để bảo toàn ngữ nghĩa các đoạn hội thoại hoặc quy định.
2. [x] Search: Nâng cấp lên Hybrid Search (BM25 + Dense Qdrant + RRF) để bù đắp điểm yếu của Vector khi tìm từ khóa cụ thể.
3. [x] Reranking: Sử dụng model `bge-reranker-v2-m3` để lọc nhiễu top 20 documents.
4. [x] Evaluation: Dùng framework RAGAS (4 metrics chính) để làm baseline benchmark trước và sau khi tối ưu.
5. [x] Enrichment: Sử dụng kỹ thuật LLM Enrichment tạo các câu hỏi giả định (HyQA) cho các tài liệu khó.

### Timeline
- Tuần 1: Nâng cấp Chunking và Database lên Qdrant cho tính năng Hybrid Search.
- Tuần 2: Tích hợp Cross-encoder Reranking và thực hiện benchmark bằng RAGAS.
- Tuần 3: Áp dụng Enrichment pipeline cho dữ liệu và thử nghiệm thực tế.
