# [Crypto] Nostalgia - NNS 2026

- **Category:** Crypto 
- **Points:** 
- **Solves:** 
- **Difficulty:** Easy 
- **Author:** 

---

## 1. Description 

<details>
<summary><b> (chal.py)</b></summary>

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
from hashlib import sha256
import time

def lcg(s):
	return (16843009*int(s)+826366247)%(2**32)

seed = time.time() # nanosecond precision, you will never find the seed muahahaha

for _ in range(1337):
	seed = lcg(seed)

key = sha256(str(seed).encode()).digest()
flag = b"NNS{???????????????????????????????}"
ct = AES.new(key, AES.MODE_ECB).encrypt(pad(flag, AES.block_size)).hex()
print(f"ct = '{ct}'")
# ct = '85c43735b8442a69843bdc2ca0fb2d41eb548057c43b912704abdf2e27a8d8bc97017ec30b5d100498f12183c9e2ebed'
```

</details>
---

## 2. Overview & Recon 
1. **Hàm sinh số giả ngẫu nhiên (LCG):** Chương trình sử dụng bộ sinh số ngẫu nhiên tuyến tính đồng dư (Linear Congruential Generator - LCG) có dạng tổng quát:

$$X_{n+1} = (a \cdot X_n + c) \pmod m$$

Trong đó:
- $a = 16843009$ (Multiplier)
- $c = 826366247$ (Increment)
- $m = 2^{32}$ (Modulus)

2. **Khởi tạo Seed & Lỗ hổng PRNG:**
- Mặc dù tác giả chú thích `time.time()` có độ chính xác nanosecond, nhưng dòng `int(s)` trong hàm `lcg()` đã vô tình ép kiểu timestamp về **số nguyên (seconds)**. 
- Điều này làm giảm không gian tìm kiếm (Entropy) của `seed` xuống mức rất nhỏ. Nếu khoanh vùng được thời điểm giải đấu diễn ra, ta hoàn toàn có thể vét cạn (Bruteforce) giá trị `timestamp` theo từng giây.

3. **Thuật toán Mã hóa:**
- `seed` sau khi đi qua LCG 1337 lần sẽ được băm SHA-256 để tạo key 256-bit.
- Flag được mã hóa bằng thuật toán **AES-128-ECB** với PKCS#7 Padding.


---

## 3. Mathematical Proof / Analysis 
- Hàm lcg sau n lần lồng hàm có dạng
	 $X_n = \Big( a^n \cdot X_0 + c \cdot (1 + a + a^2 + \dots + a^{n-1}) \Big) \bmod m$

  Hay
  $X_n = \Big( A \cdot X_0 + c \cdot S \Big) \bmod m$
  
  $A= a^n$
  
  $S=(1 + a + a^2 + \dots + a^{n-1})$

- Tính nhanh $a^n$ bằng Binary Exponentiation

Thay vì nhân số $a$ với chính nó $n$ lần (rất chậm khi $n$ lớn), ta dùng cách nhân đôi liên tục.

Chuyển $n$ sang nhị phân.

Bắt đầu từ bit thấp nhất (bên phải).

Mỗi lần:
Nếu bit = 1 → nhân kết quả với $a$ hiện tại.

Luôn bình phương $a$ lên để chuẩn bị cho bit tiếp theo.

Lặp cho đến hết các bit.

- Quy trình giải mã AES-ECB:
$$\text{Plaintext} = \text{unpad}(\text{AES}_{ECB}\text{-Decrypt}(\text{ct}, \text{key}))$$
Giải mã 
```python
ct = AES.new(key, AES.MODE_ECB).encrypt(pad(flag, AES.block_size)).hex()
```
- pad - unpad

- encrypt- decrypt
```python
pt=unpad(AES.new(key, AES.MODE_ECB).decrypt(ct), AES.block_size)
```


---

## 4. Exploitation / Solution 

```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad
from hashlib import sha256
from datetime import datetime
a= 16843009
c= 826366247
m= 2**32
ct = bytes.fromhex('85c43735b8442a69843bdc2ca0fb2d41eb548057c43b912704abdf2e27a8d8bc97017ec30b5d100498f12183c9e2ebed')
def modpow(base, exp, mod): ##Hàm tính lũy thừa nhanh bằng Binary Exponentiation
	result=1
	base%=mod
	while exp>0:
		if exp &1:
			result=(result*base)%mod
		base=(base*base)%mod
		exp >>=1
	return result
A=modpow(a,1337,m)
S=0
power=1
for _ in range(1337):##Tính tổng S 
	S=(S+power)%m
	power=(power*a)%m
start_ts =int(datetime(2026,9,1).timestamp())
end_ts=int(datetime(2026,9,5).timestamp())

for ts in range( end_ts, start_ts, -1 ): 
	seed= (A*ts+c*S)%m
	key=sha256(str(seed).encode()).digest()
	try:
		pt=unpad(AES.new(key, AES.MODE_ECB).decrypt(ct),16)
		if pt.startswith(b'NNS{') and pt.endswith(b'}'):
			print(pt.decode())
			break
	except:
		pass

```
## 5. Flag
 NNS{th3_b3st_t1m3_t0_m4k3_m3m0r13s_15_n0w}
