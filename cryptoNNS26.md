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
-Hàm lcg viết tắt của Linear Congruential Generator (Bộ sinh số giả ngẫu nhiên tuyến tính đồng dư).
có dạng $$X_{n+1} = (a \cdot X_n + c) \bmod m$$
Trong đó:

$X_n$: số hiện tại (số giả ngẫu nhiên thứ $n$)
$X_{n+1}$: số tiếp theo
$a$: hệ số nhân (multiplier)
$c$: số cộng thêm (increment / offset)
$m$: modulus (thường là số nguyên lớn)


---

## 3. Mathematical Proof / Analysis 
- Hàm lcg sau n lần lồng hàm có dạng
	 $X_n = \Big( a^n \cdot X_0 + c \cdot (1 + a + a^2 + \dots + a^{n-1}) \Big) \bmod m$
- Tính nhanh a^n bằng Binary Exponentiation
Thay vì nhân số $a$ với chính nó $n$ lần (rất chậm khi $n$ lớn), ta dùng cách nhân đôi liên tục.
Chuyển $n$ sang nhị phân.
Bắt đầu từ bit thấp nhất (bên phải).
Mỗi lần:
Nếu bit = 1 → nhân kết quả với $a$ hiện tại.
Luôn bình phương $a$ lên để chuẩn bị cho bit tiếp theo.

Lặp cho đến hết các bit.




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
##Hàm tính a^n bằng Binary Exponentiation
def modpow(base, exp, mod):
	result=1
	base%=mod
	while exp>0:
		if exp &1:
			result=(result*base)%mod
		base=(base*base)%mod
		exp >>=1
	return result
##Hàm tính lcg từ A=a^n và S=(1 + a + a^2 + \dots + a^{n-1})
def lcg(a,c,n,m,x0):
	A=modpow(a,n,m)
	S=0
	power=1
	for _ in range(n):
		S=(S+power)%m
		power=(power*a)%m
	return (A*x0+c*S)%m
start_ts =int(datetime(2026,9,1).timestamp())
end_ts=int(datetime(2026,9,4).timestamp())

for ts in range( end_ts, start_ts, -1 ): 
	seed= lcg(a,c,1337,m,ts)
	key=sha256(str(seed).encode()).digest()
	try:
		pt=unpad(AES.new(key, AES.MODE_ECB).decrypt(ct),16)
		if pt.startswith(b'NNS{') and pt.endswith(b'}'):
			print(pt.decode())
			break
	except:
		pass
```
