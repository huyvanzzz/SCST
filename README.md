# Optimizing Remote Sensing Image Captioning with SCST

Dự án này tập trung vào việc tối ưu hóa khả năng sinh mô tả ảnh viễn thám (RSIC) bằng kỹ thuật **Self-Critical Sequence Training (SCST)**. Mục tiêu cốt lõi là chứng minh rằng việc tối ưu hóa trực tiếp các chỉ số đánh giá (Metric-based Optimization) hiệu quả hơn hẳn phương pháp huấn luyện truyền thống.

## Self-Critical Sequence Training (SCST)

Trong bài toán Image Captioning, các mô hình thường được huấn luyện bằng hàm loss **Cross-Entropy (XE)**. Tuy nhiên, XE mắc phải hai vấn đề lớn:

1. **Exposure Bias:** Sự khác biệt giữa từ ngữ mô hình thấy khi học và khi suy luận thực tế.
2. **Metric Mismatch:** Tối ưu hóa xác suất từ tiếp theo không đồng nghĩa với việc tối ưu hóa chất lượng toàn bộ câu (CIDEr, BLEU).

**Giải pháp SCST trong dự án này:**
Chúng tôi áp dụng Reinforcement Learning (Học tăng cường) để tinh chỉnh mô hình:

* **Cơ chế Reward:** Sử dụng chỉ số **CIDEr** làm phần thưởng (Reward) trực tiếp.
* **Self-Critical Baseline:** Mô hình tự so sánh câu sinh ra từ chiến thuật *Greedy Search* với câu lấy mẫu ngẫu nhiên để xác định hướng cập nhật trọng số, giúp giảm phương sai và tăng độ ổn định khi hội tụ.

---

## Thực nghiệm & Chứng minh

Để chứng minh sức mạnh của SCST, chúng tôi đã triển khai kỹ thuật này trên hai nền tảng kiến trúc khác nhau trên bộ dữ liệu **RSICD**. Kết quả cho thấy SCST luôn tạo ra sự đột phá về hiệu suất bất kể kiến trúc cốt lõi là gì.

### 1. Hiệu quả trên các kiến trúc (Test set)

| Kiến trúc | Phương pháp huấn luyện | BLEU-4 | ROUGE-L | **CIDEr (Cải thiện)** |
| --- | --- | --- | --- | --- |
| **CNN-LSTM** | Cross-Entropy (Baseline) | 19.97 | 38.77 | 42.9 |
| **CNN-LSTM** | **With SCST** | 23.06 | 44.91 | **54.6 (+27%)** |
| **ViT-Transformer** | Cross-Entropy (Baseline) | 26.21 | 50.78 | 69.8 |
| **ViT-Transformer** | **With SCST** | 27.40 | 51.29 | **71.9 (+3%)** |

## Cơ chế triển khai SCST

Quá trình huấn luyện được chia làm 2 giai đoạn:

1. **Pre-training:** Huấn luyện mô hình với hàm loss Cross-Entropy cho đến khi hội tụ để tạo nền tảng ngôn ngữ.
2. **SCST Fine-tuning:** Tối ưu hóa hàm mục tiêu dựa trên kỳ vọng phần thưởng.
