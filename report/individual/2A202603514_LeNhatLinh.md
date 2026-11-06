# Báo cáo cá nhân — Day 01: Lọc tin nhắn rác bằng ML, DL, Embedding và LLM

- **MSSV:** 2A202603514
- **Họ và tên:** Lê Nhật Linh
- **SEED:** 5303
- **Repo:** https://github.com/NhatLinh056/K5-L3A-Day-01-LeNhatLinh-2A202603514-LLMFoundation

## 1. Bằng chứng

| Tệp | Có? |
| --- | --- |
| [`day01_lab.ipynb`](day01_lab.ipynb) — notebook đã chạy, còn output | ☑ |
| [`lab1_report.json`](lab1_report.json) — do ô "Báo cáo cuối" sinh ra | ☑ |
| [`hand_calc.jpg`](hand_calc.jpg) — ảnh tính tay có các bước | ☑ |

## 2. Kết quả chính

| Phương pháp | F1 Test | F1 SUB 40 | p95 (ms/tin) | USD / 1k tin |
| --- | :---: | :---: | :---: | :---: |
| **TF-IDF + Naive Bayes** | 0.93 | 0.88 | 1.2 | $0.000 |
| **TF-IDF + Logistic Reg** | 0.94 | 0.90 | 1.5 | $0.000 |
| **Bi-LSTM** | 0.92 | 0.86 | 15.4 | $0.000 |
| **Embedding (Sentence-BERT)** | 0.95 | 0.92 | 28.6 | $0.000 |
| **LLM (Few-shot)** | 0.96 | 0.95 | 650.0 | $0.150 |

## 3. Dự đoán và thực tế

1. **TF-IDF vs Bi-LSTM:** Đoán Bi-LSTM vượt trội TF-IDF, thực tế TF-IDF ngang ngửa (F1 ~0.94 vs 0.92) do SMS ngắn và ít phụ thuộc thứ tự từ.
2. **Tốc độ LLM:** Đoán LLM chạy dưới 100ms, thực tế trễ >600ms vì nghẽn mạng và thời gian sinh token.
3. **Few-shot vs Zero-shot:** Đoán Few-shot tăng mạnh F1, thực tế chỉ tăng nhẹ (~1-2%) nhưng tốn thêm gấp đôi token.

## 4. Phân tích 50 case khó

- **Model tụt nhiều nhất:** Naive Bayes tụt mạnh nhất ở nhóm tin nhắn dùng từ lóng, teencode và tin nhắn giả mạo tinh vi.
- **Case 1 (False Negative):** Tin lừa đảo ngân hàng không chứa từ khóa spam truyền thống $\rightarrow$ TF-IDF bỏ sót vì thiếu từ khóa.
- **Case 2 (False Positive):** Tin nhắn bạn bè gửi mã OTP hoặc link chia sẻ $\rightarrow$ Model gán nhãn nhầm là spam do chứa URL và số.

## 5. Thử thách cá nhân

- **Đề bài:** Challenge #6 (SEED % 6 = 5) — So sánh Prompt định dạng JSON vs Prompt 1 từ.
- **Cách làm:** Viết hàm prompt yêu cầu trả về JSON `{"label": "spam"/"ham"}`, dùng `json.loads` để parse nhãn và so sánh với prompt 1 từ trên 10 tin nhắn.
- **Kết quả:** F1 tương đương nhau (~0.93), nhưng prompt JSON đạt độ ổn định định dạng 100%, không bị sinh chữ thừa dù chậm hơn một chút.

## 6. Phương án cho sản phẩm thật

- **Lựa chọn:** Dùng kiến trúc lai (Router) — Lớp đầu dùng TF-IDF + Logistic Regression lọc nhanh các tin chắc chắn (< 2ms), chỉ đẩy tin nghi ngờ sang LLM.
- **Dẫn chứng:** Đáp ứng chuẩn p95 < 50ms, giảm 90% chi phí API so với dùng LLM thuần túy.
- **Đánh đổi:** Chấp nhận độ trễ ở một tỷ lệ nhỏ tin nhắn khó để giữ chi phí vận hành ở mức tối thiểu.

## 7. AI log và điều đã học

| Phần | Bạn hỏi AI gì (prompt) | AI trả lời/viết gì | Bạn đã sửa / kiểm chứng gì |
| --- | --- | --- | --- |
| **Phần 4: LLM** | Cách viết `parse_label` và `call_openai` | Code regex lọc nhãn và gọi API | Chạy unit test pass 100% |
| **Phần 5: Tính tay** | Cách tính 5 câu lý thuyết | Công thức và số liệu | Kiểm tra khớp mã SHA-256 |

- **Điều nhận ra:** Không phải lúc nào Deep Learning hay LLM cũng là giải pháp tốt nhất. Với bài toán tin nhắn ngắn, ML cổ điển vừa nhanh, vừa rẻ mà hiệu quả không hề thua kém.

