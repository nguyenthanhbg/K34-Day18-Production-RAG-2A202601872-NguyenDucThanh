# Group Report - Lab 18: Production RAG

**Nhóm:** Cá nhân
**Ngày:** 2026-08-19

## Thành viên & Phân công

| Tên | Module | Hoàn thành | Tests pass |
|-----|--------|-----------|-----------|
| Nguyễn Đức Thành | M1: Chunking | 100% | 8/8 |
| Nguyễn Đức Thành | M2: Hybrid Search | 100% | 5/5 |
| Nguyễn Đức Thành | M3: Reranking | 100% | 5/5 |
| Nguyễn Đức Thành | M4: Evaluation | 100% | 4/4 |
| Nguyễn Đức Thành | M5: Enrichment | 100% | 15/15 |

## Kết quả RAGAS

| Metric | Production Score |
|--------|------------------|
| Faithfulness | 0.7780 |
| Answer Relevancy | 0.3863 |
| Context Precision | 0.9583 |
| Context Recall | 0.8417 |

## Kết luận
Hoàn thành 100% các modules và vượt qua toàn bộ 37/37 unit tests. Pipeline RAG đã chạy thành công từ bước chunking đến bước đánh giá với các điểm số Retrieval (Precision và Recall) đạt mức rất cao.
