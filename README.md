# 📊 Ước Lượng Khoảng Tin Cậy 95% Cho Kết Quả Học Tập Học Sinh
> **Học phần:** Suy luận thống kê (MI3031) – Nhóm 4  
> **Ngôn ngữ:** Python 3.10+ | **Bộ dữ liệu:** Kaggle Students Performance in Exams

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange.svg)](https://jupyter.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

---

## 📌 1. Giới thiệu đề tài
Dự án thực hiện phân tích thống kê suy luận và ước lượng khoảng tin cậy (KTC) 95% cho các tham số tổng thể dựa trên mẫu khảo sát **1.000 học sinh** (`StudentsPerformance.csv` từ Kaggle), bao gồm 4 bài toán:
1. **Câu a:** KTC 95% cho kỳ vọng điểm môn Toán ($\mu$).
2. **Câu b:** KTC 95% cho phương sai điểm môn Toán ($\sigma^2$).
3. **Câu c:** KTC 95% cho tỷ lệ học sinh hoàn thành khóa học ôn tập ($p$).
4. **Câu d:** KTC 95% cho hiệu kỳ vọng điểm môn Toán giữa nhóm có và không ôn tập ($\mu_1 - \mu_2$).

---

## 🎯 2. Nguyên tắc thực hiện
Theo yêu cầu chặt chẽ của môn học, mỗi bài toán đều tuân thủ cấu trúc 4 phần:
1. **Công thức toán học:** Trình bày rõ công thức ước lượng điểm, sai số chuẩn và khoảng tin cậy.
2. **Điều kiện áp dụng:** Kiểm tra kích thước mẫu ($n$), tính độc lập và phân phối chuẩn (kiểm định phân phối ở câu b).
3. **Tính toán từng bước (From Scratch):** Tự lập trình công thức tính tổng, trung bình mẫu $\bar{X}$, độ lệch chuẩn mẫu $S$, không gọi các hàm tính sẵn như `.mean()`, `.std()`.
4. **Tra phân vị:** Thư viện `scipy.stats` chỉ được sử dụng duy nhất cho mục đích tra giá trị tới hạn / phân vị ($z$, $\chi^2$, $t$).
5. **Diễn giải kết quả:** Giải thích ý nghĩa thực tế theo ngữ cảnh dữ liệu học tập.

---

## 📈 3. Bảng tổng hợp kết quả (Khoảng tin cậy 95%)

| STT | Tham số tổng thể | Biến dữ liệu | Ước lượng điểm | Cận dưới (95%) | Cận trên (95%) | Phương pháp suy luận |
| :---: | :--- | :--- | :---: | :---: | :---: | :--- |
| **a** | **Kỳ vọng điểm Toán ($\mu$)** | `math score` | 66.089 | **65.153** | **67.025** | $z$ – Mẫu lớn (CLT, $n=1000$) |
| **b** | **Phương sai điểm Toán ($\sigma^2$)** | `math score` | 229.914 | **211.782** | **250.316** | Phân phối $\chi^2$ ($df=999$) |
| **c** | **Tỷ lệ hoàn thành ôn tập ($p$)** | `test preparation course` | 35.80% | **32.83%** | **38.77%** | $z$ – Xấp xỉ chuẩn (CLT) |
| **d** | **Hiệu kỳ vọng ($\mu_1 - \mu_2$)** | `math score` theo nhóm ôn tập | 5.618 | **3.715** | **7.520** | $z$ – Hai mẫu lớn độc lập (CLT) |

> ⚠️ **Lưu ý diễn giải chuẩn xác suất:** Khoảng tin cậy 95% có nghĩa là: nếu lặp lại quy trình lấy mẫu ngẫu nhiên độc lập 1.000 học sinh nhiều lần trong cùng điều kiện, thì khoảng 95% các khoảng tin cậy dựng được sẽ bao phủ tham số thực của tổng thể. **Không diễn giải** rằng *"có 95% học sinh có điểm nằm trong khoảng này"*.

---

## 🔬 4. Tóm tắt phương pháp từng câu

* **Câu a ($\mu$):** Cỡ mẫu $n = 1000 \ge 30$, theo Định lý giới hạn trung tâm (CLT), đại lượng thống kê hội tụ chuẩn:  
  $$Z = \frac{\bar{X} - \mu}{S / \sqrt{n}} \xrightarrow{d} N(0, 1) \implies \text{KTC: } \bar{X} \pm z_{\alpha/2} \frac{S}{\sqrt{n}}$$

* **Câu b ($\sigma^2$):** Khoảng $\chi^2$ đòi hỏi giả định tổng thể có phân phối chuẩn:  
  $$\frac{(n-1)S^2}{\sigma^2} \sim \chi^2(n-1) \implies \text{KTC: } \left[ \frac{(n-1)S^2}{\chi^2_{\alpha/2, n-1}}, \frac{(n-1)S^2}{\chi^2_{1-\alpha/2, n-1}} \right]$$  
  *(Đã kiểm định tính chuẩn bằng D’Agostino’s $K^2$ test; thảo luận tính nhạy cảm của kiểm định khi $n$ lớn).*

* **Câu c ($p$):** Tỷ lệ học sinh hoàn thành khóa ôn tập ($X = 358, n = 1000$). Thỏa mãn điều kiện $n\hat{p} \ge 5$ và $n(1-\hat{p}) \ge 5$:  
  $$\text{KTC: } \hat{p} \pm z_{\alpha/2} \sqrt{\frac{\hat{p}(1-\hat{p})}{n}}$$

* **Câu d ($\mu_1 - \mu_2$):** So sánh giữa nhóm đã học ôn ($n_1 = 358$) và không học ôn ($n_2 = 642$). Cả 2 mẫu đều lớn độc lập:  
  $`\text{KTC: } (\bar{X}_1 - \bar{X}_2) \pm z_{\alpha/2} \sqrt{\frac{S_1^2}{n_1} + \frac{S_2^2}{n_2}}`$  
  *(Cả 2 cận đều dương chứng minh khóa ôn tập có cải thiện điểm thi rõ rệt ở mức ý nghĩa 5%).*

---

## 📂 5. Cấu trúc thư mục
```text
MI3031-Statistical-Inference/
├── StudentsPerformance.csv     # Dữ liệu gốc Kaggle (1.000 dòng, 8 cột)
├── MI3031_Nhom4.ipynb          # Jupyter Notebook chi tiết code và diễn giải
├── requirements.txt            # Thư viện sử dụng (numpy, pandas, scipy)
└── README.md                   # Báo cáo tóm tắt đề tài