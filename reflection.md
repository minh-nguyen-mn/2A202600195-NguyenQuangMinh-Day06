# Individual reflection — Nguyễn Quang Minh (2A202600195)

## 1. Role
Test engineer + data researcher. Phụ trách xây dựng evaluation pipeline cho RAG system, thiết kế dataset test, và hỗ trợ research nguồn dữ liệu chính sách cho chatbot XanhSM.

---

## 2. Đóng góp cụ thể
- Xây dựng toàn bộ evaluation pipeline (`test.py`, `test_retrieval.py`) để chạy RAG system và đo accuracy, overlap, latency
- Thiết kế và tạo `test_dataset.json` từ nhiều nguồn policy nội bộ (thu nhập, vận hành, taxi rules, S2S camera, v.v.)
- Implement LLM-as-a-judge trong `src/evaluator.py` để cải thiện đánh giá từ rule-based → semantic evaluation
- Thiết kế logging system (`extras/prompt-test-log.md`, `results.json`, `report.txt`) để debug và phân tích lỗi model
- Debug và cải tiến metric evaluation (soft match + keyword overlap + LLM scoring hybrid)
- Hỗ trợ research và tổng hợp data sources (policy documents, taxi rules, driver guidelines) để xây dựng knowledge base cho RAG system

---

## 3. SPEC mạnh/yếu
- **Mạnh nhất: Evaluation design & feedback loop**

  Nhóm ban đầu chỉ dùng accuracy rule-based, nhưng tôi mở rộng thành hybrid evaluation:
  - soft match (keyword-based)
  - overlap scoring
  - LLM judge (semantic correctness)

  Điều này giúp hệ thống đánh giá đúng hơn trong context policy QA, nơi câu trả lời có thể đúng ý nhưng khác wording.

- **Yếu nhất: Retrieval debugging depth**

  Ban đầu chưa log đầy đủ retrieval context (top-k chunks), nên khó phân tích root cause của một số lỗi hallucination. Nếu làm lại sẽ thêm tracing layer từ retriever.

---

## 4. Đóng góp khác
- Thiết kế format log markdown để team dễ đọc và debug từng prediction
- Sửa `test_retrieval.py` để đảm bảo consistency với evaluation pipeline chính
- Giúp team hiểu cách đọc LLM judge score và cách convert sang binary accuracy
- Tham gia review dataset để đảm bảo câu hỏi phủ đủ các nhóm policy (thu nhập, vận hành, camera, an toàn)

---

## 5. Điều học được
Trước project, tôi nghĩ evaluation chỉ là tính accuracy đơn giản giữa prediction và ground truth.

Sau project này, tôi hiểu rằng:
- Với RAG system, **evaluation quan trọng hơn model**
- Rule-based metrics rất dễ sai lệch vì paraphrase trong ngôn ngữ tự nhiên
- LLM-as-a-judge giúp capture semantic correctness tốt hơn nhưng cần calibration (bias + threshold)
- Logging chi tiết (prompt, response, latency, score) là bắt buộc để debug real-world AI system

Quan trọng nhất: **evaluation không chỉ là kỹ thuật, mà là cách định nghĩa “đúng” cho sản phẩm AI**

---

## 6. Nếu làm lại
- Sẽ thiết kế retrieval tracing ngay từ đầu (top-k chunks + similarity scores)
- Thêm confusion analysis dashboard để phân loại lỗi (hallucination vs retrieval failure vs prompt issue)
- Chuẩn hóa dataset sớm hơn để tránh inconsistency giữa các policy sources
- Test song song nhiều version prompt thay vì iterate một pipeline duy nhất

---

## 7. AI giúp gì / AI sai gì
- **Giúp:**
  - Dùng GPT/Claude để brainstorm edge cases trong evaluation (ví dụ: paraphrase policy questions)
  - Hỗ trợ viết LLM judge prompt để đánh giá semantic correctness
  - Debug logic metric (overlap vs semantic mismatch)

- **Sai/mislead:**
  - AI judge đôi khi over-penalize câu trả lời đúng nhưng khác wording → cần chỉnh threshold
  - Suggest đơn giản hóa evaluation pipeline quá mức → nếu làm theo sẽ mất khả năng detect failure modes phức tạp
  - Một số gợi ý về architecture logging chưa phù hợp với scale RAG → phải chỉnh lại theo hướng lightweight logging
