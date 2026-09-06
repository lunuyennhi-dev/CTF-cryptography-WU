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



---

## 3. Mathematical Proof / Analysis (Phân tích Toán học & Lý thuyết)





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
def modpow(base, exp, mod):
	result=1
	base%=mod
	while exp>0:
		if exp &1:
			result=(result*base)%mod
		base=(base*base)%mod
		exp >>=1
	return result
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
