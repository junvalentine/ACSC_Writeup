# Dual Signature Algorithm

## Information
**Category** | **Points** 
--- | --- 
Crypto | 250 

## Description

It must be twice as powerful as the ordinal one.

## Solution

At first, i found this [article](https://cseweb.ucsd.edu/classes/sp07/cse206a/lecture9.pdf) but i couldn't find anything. Then i found a way to combine two given modular equation in sign functions, which is $s_1=k^{-1}(z+r_1x) [q_1]$ and $s_2=k^{-1}(z+r_2x) [q_2]$

Using CRT, we can compute $S$ such that $S=s_1[q_1]$ and $S=s_2[q_2]$, and $R$ such that $R=r_1[q_1]$ and $R=r_2[q_2]$. Then the two equation mention above can be rewrite to $S=k^{-1}(z+Rx) [q_1q_2]$. Observe that $x,k$ is around 512 bits, while $N=q_1q_2$ is around 1040 bits, we can use LLL to get shortest vector $(\alpha * k, \beta * x, 1,0)$, where $\alpha$ and $\beta$ are scaling factors.

```python
from Crypto.Util.number import *
from hashlib import *

r1, s1 = 2059408995750136677433298244389263055046695445249968690077607175900623237060138734944126780231327500254319039236115174790677322287273023749694890125234033630, 705204023016308665771881112578269844527040578525414513229064579516151996129198705744493237004425745778721444958494868745594673773644781132717640592278534802
r2, s2 = 3246603518972133458487019157522113455602145970917894172952170087044203882577925192461339870709563972992589487629762432781841010769867505736764230484818447604, 2142497127325776381345617721109438439759390966544000203818908086062572965004742554536684765731611856029799528558073686810627789363181741779462572364133421373
p1, p2 = 6276170351477662358610296265757659534898563584329624403861678676207084984210281982964595245398676819568696602458985212398017251665201155991266054305219383699, 6592790035600261324619481304533463005761130886111654202136347967085156073379713687101783875841638513262245459729322943177912713281466956529743757383039213839
q1, q2 = (p1 - 1) // 2, (p2 - 1) // 2
N = q1*q2
m = b"omochi mochimochi mochimochi omochi"
m= int(int(sha256(m).hexdigest(), 16) % N)

S=int(CRT_list([s1,s2],[q1,q2]))
R=int(CRT_list([r1,r2],[q1,q2]))


A = Matrix([
    [1/2^512,0,0,-S],
    [0,1/2^512,0, R ],
    [0,0,1, m ],
    [0,0,0,N]

])
res = int(A.LLL()[0][1]*(2^512))
print(long_to_bytes(res))
```
> Flag is: ACSC{okay_you_must_be_over_twice_as_powerful_as_the_DSA}'
