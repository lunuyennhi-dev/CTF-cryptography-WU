# [Crypto] Nostalgia - NNS 2026

- **Category:** Crypto 
- **Points:** 
- **Solves:** 
- **Difficulty:** Easy 
- **Author:** 

---

## 1. Description (Mô tả đề bài)

**File đính kèm:** `chal.py`, `output.txt`  
**Connection:** `nc challenge.ctf.com 1337` *(nếu là bài kết nối máy chủ)*

---

## 2. Overview & Recon (Tổng quan & Phân tích)

*Nêu nhận xét ban đầu khi mở đề bài hoặc xem file source code:*
- Phân tích cấu trúc file đính kèm hoặc luồng hoạt động của chương trình.
- Tìm ra điểm bất thường, thuật toán yếu, hoặc lỗ hổng chính (ví dụ: hằng số $e=3$ quá nhỏ, dính lỗi Padding Oracle, dùng lại IV trong AES-CTR...).

---

## 3. Mathematical Proof / Analysis (Phân tích Toán học & Lý thuyết)

*(Phần này cực kỳ quan trọng đối với Crypto!)*
Trình bày các công thức toán học hoặc các bước biến đổi logic để đi đến cách giải:

$$c \equiv m^e \pmod{n}$$

Vì $m^e < n$ nên ta có thể khai căn trực tiếp trên tập số thực:

$$m = \sqrt[e]{c}$$

---

## 4. Exploitation / Solution (Khai thác & Code giải)

Mô tả ngắn gọn các bước thực hiện mã khai thác, sau đó dán đoạn script Python/SageMath hoàn chỉnh:

```python
from Crypto.Util.number import long_to_bytes
import gmpy2

# 1. Khai báo các dữ kiện từ đề bài
c = 123456789...
e = 3

# 2. Thực hiện tính toán/giải mã
m, exact = gmpy2.iroot(c, e)

# 3. In ra kết quả
if exact:
    print("[+] Flag:", long_to_bytes(m).decode())
