# Fermat's Attack (close primes)
Fermet's Attack exploits the fact that the two primes **p and q** are too close to each other.     
how much is too close? if **p - q < N^1/4** then you can attack it     
how?     
because **p** is close to **q** then **N** is almost **p^2**     
so Fermet's says:     
- N = p * q
-   = [(p - q) / 2]^2 * [(p + q) / 2]^2
-   = x^2 - y^2
-   = (x - y) * (x + y)
which implies
- x^2 = N + y^2
assume that y^2 = 0
- x = sqrt(N)        

which is a lower bound for **x**, now we calculate **y** using ```y^2 = x^2 - N```      
and check if what we got (y^2) is perfect square      
if its a perfect square we are done `p = x - y` and `q = N / p`     
if not, then we add 1 to x and recalculate, why? because N^2 is a *lower bound* for x, why? `x = sqrt(N + y^2)` we know `y != 0` (two primes and such), so we know `x > sqrt(N)`      

```python
y_2 = 0
x = int(sqrt(N))
while True:
    y_2 = x^2 - N 
    if sqrt(y_2) % 1 == 0: # is prefect square
        break
    x += 1
```

this should give you both x and y and whats left is to get `q` and `p`.     

#### Example
our humble primes
```python3
p = 294096520707427240349164810891573616029 
q = 294096520707427240429856651418944781973
N = p * q
(p - q) < N**(1/4)
>>> True
```
the fermat attack
```python
import gmpy

x = gmpy.sqrt(N)
y = 0
while True:
    y = x**2 - N
    if y > 0 and y == gmpy.sqrt(y) ** 2: # is prefect square
        break
    x += 1

y = gmpy.sqrt(y)
(x - y) == p
# >>> True
(x + y) == q
# >>> True
```

- hint: use gmpy for General MultiPrecision arithmetic for Python aka big integers      
for a more in depth explanation check out [bitsdeep](https://bitsdeep.com/posts/attacking-rsa-for-fun-and-ctf-points-part-2/)