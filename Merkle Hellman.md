# Merkle Hellman

## Information
**Category** | **Points** 
--- | --- 
Warmup + Crypto | 50 

## Description

We tired of RSA, try a new cryptosystem by merkle and hellman but we don't know how to decrypt the ciphertext.

We need your help for decrypt the ciphertext to get back my flag.txt!

## Solution

Looking at the source, we can see that the key that encrypt function uses is given, and we can brute force from 0 to 2^7=128 the ascii value of each character of the flag

```python
from Crypto.Util.number import *

pk = [7352, 2356, 7579, 19235, 1944, 14029, 1084]
ct = [8436, 22465, 30044, 22465, 51635, 10380, 11879, 50551, 35250, 51223, 14931, 25048, 7352, 50551, 37606, 39550]
flag=b''
for i in ct:
    for j in range(128):
        s = 0
        for k in range(7):
            if j & (64 >> k):
                s += pk[k]
        if i==s:
            flag+=long_to_bytes(j)
print(flag)
```


> Flag is: ACSC{E4zY_P3@zy}
