# Same Modules
Imagine a communication server that uses that same modules to encrypt the messages    
you can use this fact to decrypt the messages, there are two sides for this attack    
you can either be a 
- external attacker: you need to intercpet same message twice (N, e1,c1) and (N, e2,c2) and c1 = M^e1[N] ,c2 = M^e2[N]. your goal is to get M
- internal attacker: you will need (N,d,e) and you can decrypt any messege

#### As A External Attacker
lets say you wanna decrpyt a message M and you have two public keys (N,e1) and (N,e2) in addition to thier cipher text c1 , c2.    

from the extended euclidean algo aka EEA we know e1 * u + e2 * v = gcd(e1,e2), if e1 and e2 are coprime that means gcd(e1,e2) = 1   
and we know that c1 = M^e1[N] ,c2 = M^e2[N]     
- c1^v = M^(e1 * v)[N]
- c2^u = M^(e2 * u)[N]
- c1^u * c2^v = M^(e1 * u + e2 * v)[N] = M[N]
and that's it, basic math

#### Example
```python
N = 9648882745798009001071538070762320109623176130536906107205824951560774658947

e1 = 11
c1 = 4201693885513214327213224400857517917118879542781286744140134296022154337195 

e2 = 17
c2 = 6080972146800009061925078538637959094372297860015510635995331149004755416685 

(N,e1)
>>> (9648882745798009001071538070762320109623176130536906107205824951560774658947,11)
(N,e2)
>>> (9648882745798009001071538070762320109623176130536906107205824951560774658947,17) 
```

```python
from sage.all import *

(_,u,v) = xgcd(e1,e2) # retuns (gcd,u,v) comes from sage
(u,v)
>>> (-3, 2)
```

now we have (u , v) we just use powers , and the gcd to get **M**    

```python
C1 = pow(c1,u,N)
C2 = pow(c2,v,N)
M = bytes.fromhex(hex((C1 * C2) % N)[2:]).decode()
M
>>> 'RSA is easy, kinda of :)'
```



#### As A Internal Attacker
if you are a internal attacker that means you can send messages and comunicate and stuff,    
doesnt matter what matters is you have your own public and private keys.    
we know from textbook rsa:  


- euler totient function phi = tot(n) = (p - 1)  * (q - 1)[N] 
- e * d = 1[phi] => e * d - 1 = 0[phi] => e * d - 1 = k * **phi**
so our goal here is to get the value of **phi**    
- k = (e * d - 1) / **phi**, **phi** is close to N so we just replace it 
- k = (e * d - 1) / N  
the we calculate k, and use it to get **phi**
- **phi** = (e * d - 1) / k, then check e * d = 1[phi]. if its not true inc k and retry (because when we divided by N we undershot the value of k)    

and that we have **phi** we can take any message public key (N,e1) calculate d1 and decrypt the message.    

#### Example

you have your own private/public key (N,e) and (N,d)
```python
N = 12466403138947673902957276265996317921445277021982021649306756912473380350423 
e = 17
d = 10999767475542065208491714352349692283359047305255038740222233537532209338353 
```

from this calculate **phi**
```python
k = (e * d - 1) // N 
k
>>> 14

phi = (e * d - 1) // k
phi
>>> 13356860506015364896025653142138912058364557442095404184555569295574825625142
```

check that phi is correct, if not just add one untill its correct
```python
(e * d)  % phi
>>> 13

k += 1
phi = (e * d - 1) // k
(e * d)  % phi
>>> 1
```

and there you go, you have phi. lets use it to decrypt a msg    
```python
e1 = 17 
c1 = 4278104645824802693158656653941975614276967946744859214311638245663478803459

d1 = pow(e1,-1,phi)

M = bytes.fromhex(hex((C1 * C2) % N)[2:]).decode()
M
>>> "Spy from the inside"
```

you can even calculate the factors of N after you get phi     
not doing that here, but take this hint
- phi = (p - 1) * (q - 1) = p * q - p - q + 1 = N - p - q + 1 => p + q = N - phi + 1 = **A**
- N = p * q = **B**
- now solve the syatem **x^2 - A * x + B** to get p, q = N / q



#### Again all the credit goes to [bitsdeep](https://bitsdeep.com/posts/attacking-rsa-for-fun-and-ctf-points-part-1/)