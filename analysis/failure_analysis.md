# Failure Analysis - Lab 18: Production RAG

**Nhóm:** Cá nhân
**Thành viên:** Nguyễn Đức Thành

---

## RAGAS Scores

| Metric | Naive Baseline | Production | Δ |
|--------|---------------|------------|---|
| Faithfulness | N/A | 0.7780 | |
| Answer Relevancy | N/A | 0.3863 | |
| Context Precision | N/A | 0.9583 | |
| Context Recall | N/A | 0.8417 | |

## Bottom-5 Failures

### #1
- **Question:** Một nhân viên Senior có 9 năm thâm niên được nghỉ bao nhiêu ngày phép năm và lương trong khoảng nào?
- **Expected:** Trả lời chính xác số ngày phép và khoảng lương dựa trên cấp bậc Senior.
- **Got:** Câu trả lời có thể bị trộn lẫn thông tin hoặc tự bịa ra khoảng lương không có trong tài liệu.
- **Worst metric:** faithfulness (0.37)
- **Error Tree:** Output sai -> Context đúng -> Query OK
- **Root cause:** LLM hallucinated (tự bịa thông tin) do context trả về chứa nhiều mảnh thông tin rời rạc về thâm niên và bảng lương.
- **Suggested fix:** Siết chặt lại System Prompt (như đã làm), giảm tham số temperature của LLM xuống 0.

### #2
- **Question:** Nhân viên tạm ứng 15 triệu, sau 20 ngày mới thanh toán. Bị phạt bao nhiêu?
- **Expected:** Nêu rõ mức phạt trễ hạn hoàn ứng theo quy định tài chính.
- **Got:** Tính toán sai mức phạt hoặc tự suy diễn quy định phạt.
- **Worst metric:** faithfulness (0.44)
- **Error Tree:** Output sai -> Context đúng -> Query OK
- **Root cause:** LLM yếu trong việc suy luận toán học và áp dụng công thức phạt từ ngữ cảnh.
- **Suggested fix:** Yêu cầu LLM chỉ trích xuất quy định thay vì tự làm toán, hoặc sử dụng model có khả năng reasoning tốt hơn.

### #3
- **Question:** Bao lâu phải đổi mật khẩu một lần?
- **Expected:** Số ngày cụ thể (ví dụ: 90 ngày) theo chính sách bảo mật.
- **Got:** Câu trả lời chung chung hoặc số ngày không có trong context.
- **Worst metric:** faithfulness (0.45)
- **Error Tree:** Output sai -> Context thiếu -> Query OK
- **Root cause:** LLM tự động trả lời bằng kiến thức nền (kiến thức bên ngoài) khi context không chứa thông tin rõ ràng.
- **Suggested fix:** Nhấn mạnh luật 'Nếu không có thông tin, CHỈ trả lời Không tìm thấy' trong prompt.

### #4
- **Question:** Mật khẩu phải có tối thiểu bao nhiêu ký tự?
- **Expected:** Chỉ trả lời ngắn gọn một con số (ví dụ: 8 ký tự).
- **Got:** Trả lời quá dài dòng, liệt kê thêm các quy định về chữ hoa, chữ thường, ký tự đặc biệt.
- **Worst metric:** answer_relevancy (0.49)
- **Error Tree:** Output rườm rà -> Context đúng -> Query OK
- **Root cause:** LLM bị 'nhiệt tình thái quá', đưa ra thông tin đúng nhưng không trúng trọng tâm (dư thừa).
- **Suggested fix:** Đổi prompt yêu cầu trả lời cực kỳ ngắn gọn và trực tiếp (đã thực hiện).

### #5
- **Question:** Nếu cần mua một chiếc laptop 30 triệu cho nhân viên mới, ai phê duyệt và cần gì từ phòng CNTT?
- **Expected:** Trả lời đúng 2 vế: Người phê duyệt (C-level/Manager) và thủ tục CNTT.
- **Got:** Bỏ sót 1 trong 2 vế của câu hỏi hoặc lạc đề sang quy trình thanh toán.
- **Worst metric:** answer_relevancy (0.54)
- **Error Tree:** Output thiếu ý -> Context đúng -> Query OK
- **Root cause:** Câu hỏi có 2 vế phức tạp, LLM mất tập trung và chỉ trả lời vế đầu.
- **Suggested fix:** Tách câu hỏi phức tạp thành 2 câu hỏi nhỏ (Query Decomposition).

## Case Study (cho presentation)

**Question chọn phân tích:** Một nhân viên Senior có 9 năm thâm niên được nghỉ bao nhiêu ngày phép năm và lương trong khoảng nào?

**Error Tree walkthrough:**
1. **Output đúng?** Không. LLM trả lời sai thông tin lương hoặc trộn lẫn logic ngày phép.
2. **Context đúng?** Có. Hệ thống truy xuất được chính xác đoạn quy định về ngày phép thâm niên và bảng lương.
3. **Query rewrite OK?** Có. Truy vấn rõ ràng, Hybrid Search + Reranker làm tốt việc đưa context lên top.
4. **Fix ở bước:** Generation (LLM Prompting).

**Nếu có thêm 1 giờ, sẽ optimize:**
- Tách (Decompose) truy vấn này thành 2 truy vấn song song: 'Senior 9 năm thâm niên nghỉ phép bao nhiêu ngày?' và 'Khoảng lương của Senior là bao nhiêu?'. Sau đó search riêng biệt và tổng hợp lại.
