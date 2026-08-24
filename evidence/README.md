# Bằng Chứng Thực Nghiệm — Day 22: LangSmith + Prompt Versioning

Học viên: **Võ Quốc Huy**

---

## 🔗 Thông tin Nộp bài (Submission Links)
- **LangSmith Project URL**: [https://smith.langchain.com/o/b4e3076f-c21a-4db2-9d9a-1a037a47afc6/projects/p/5ba41338-2488-462d-b148-0525964b1a6e](https://smith.langchain.com/o/b4e3076f-c21a-4db2-9d9a-1a037a47afc6/projects/p/5ba41338-2488-462d-b148-0525964b1a6e)
- **LangSmith Project Name**: `day22-lab`

---

## 📁 Cấu trúc Thư mục Evidence

```
evidence/
├── 01_langsmith_traces.png      # Traces table trên LangSmith Dashboard (≥ 50 traces)
├── 02_prompt_hub.png            # 2 phiên bản Prompt trên LangSmith Prompt Hub
├── 02_ab_routing_log.txt        # Console log định tuyến A/B 50 câu truy vấn (v1/v2)
├── 03_ragas_scores.png          # Bảng điểm so sánh RAGAS V1 vs V2 trên Terminal
├── 03_ragas_report.json         # Báo cáo JSON kết quả RAGAS
├── 04_pii_demo_log.txt          # Log kết quả kiểm duyệt PII (Guardrails AI)
└── 04_json_demo_log.txt         # Log kết quả tự động sửa lỗi JSON (Guardrails AI)
```

---

## 📊 Phân tích So sánh Hiệu năng Prompt V1 vs Prompt V2

### 1. Bảng số liệu chi tiết (RAGAS Metrics)

| Chỉ số (Metric) | Prompt V1 (Ngắn gọn, súc tích) | Prompt V2 (Chuyên sâu, cấu trúc) | Winner |
| :--- | :---: | :---: | :---: |
| **Faithfulness** | **0.9576** ⭐ | 0.9275 ⭐ | **V1** (+0.0301) |
| **Answer Relevancy** | **0.9446** | 0.9333 | **V1** (+0.0113) |
| **Context Recall** | **1.0000** | **1.0000** | **Hòa** (Tối đa) |
| **Context Precision** | **0.9500** | **0.9500** | **Hòa** |

---

### 2. Nhận xét & Đánh giá Chuyên sâu

1. **Về Faithfulness (Độ trung thực với Context)**:
   - Cả hai phiên bản đều vượt trội mục tiêu đề ra ($\ge 0.8$) và đạt mức xuất sắc ($\ge 0.9$).
   - **V1 đạt 0.9576 > V2 đạt 0.9275**: Prompt V1 với chỉ thị *"trả lời thật ngắn gọn, súc tích trong 2-4 câu"* giúp LLM tập trung bám sát trực tiếp vào facts trong context, giảm thiểu nguy cơ sinh thêm câu từ dẫn dắt hoặc suy diễn ngoài tài liệu.
   
2. **Về Answer Relevancy (Độ phù hợp với câu hỏi)**:
   - **V1 (0.9446) nhỉnh hơn V2 (0.9333)**: Do phong cách trả lời trực diện, câu trả lời của V1 giải quyết ngay trọng tâm câu hỏi của người dùng mà không bị loãng bởi các đoạn mở đầu/kết luận mang tính cấu trúc.

3. **Về Retrieval (Context Recall & Context Precision)**:
   - Cả 2 phiên bản đều đạt Recall tuyệt đối (**1.0000**) và Precision cao (**0.9500**), chứng minh hệ thống chunking (RecursiveCharacterTextSplitter) và retriever FAISS ($k=3$) hoạt động cực kỳ hiệu quả, cung cấp đầy đủ thông tin cần thiết để LLM trả lời chính xác.

---

## 🛡️ Đánh giá Hệ thống Guardrails AI (Nhiệm vụ 4)
- **PII Detector**: Nhận diện bằng Regex và che giấu thành công 100% các dữ liệu nhạy cảm (Email, Phone, SSN, Credit Card, Multi-PII) với hành động `OnFailAction.FIX`.
- **JSON Formatter**: Tự động khắc phục các lỗi định dạng phổ biến (loại bỏ markdown fences ` ```json `, chuẩn hóa single quotes `'` thành double quotes `"`, xóa trailing commas `,}`) và cung cấp JSON fallback an toàn khi gặp dữ liệu không thể phục hồi.
