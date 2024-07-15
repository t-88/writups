# Collection Of RSA Keys   

if a challange provided you with a list of RSA pub keys and encrypted messages, then you can attack this keys with 0.02% chance of succes, i know too much.     
a paper titled [Ron was wrong, Whit is right](https://eprint.iacr.org/2012/064.pdf) which preformed a sanity check of public keys collected from the internet found that 2 out of 1000 of this keys of the moduli are vulnerable, so about 99.8% of the RSA keys are safe.       


A very fast skim through the paper
---
there are bunch of attacks and checks that the paper talked about the main ones are:    
- Debian moduli: there was a bug in Debian random generation number algorthim which made primes in a predictable small range, easy to attack. i think there is a database of the weak moduli and such. i never used it so i cant say much
- Shared moduli: same moduli means if you break one you break all of them
- Primality, small factors, and other tests: talks about general RSA implementation weaknesses and factorisation attacks (wiener attack, fermat's ....), 
- **Moduli with shared factors**: is the attack that i have experince with, the main idea is to loop through all the gcd of the public keys, hoping to find a shared prime factor. once you find it you are able to break the encryption easily
