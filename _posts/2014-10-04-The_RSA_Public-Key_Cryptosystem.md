---
layout: post
title:  "[CSIT 5710] Lecture 09: The RSA Public-Key Cryptosystem"
date:   2014-10-04 12:00:00
tags:	tech
myTag:	security

---

# Lecture 09: The RSA Public-Key Cryptosystem

---------------------------------------------------------------------------

1. Euler's theorem.
2. RSA public-key algorithm.

## Euler’s Theorem

### Euler’s Totient Function φ(n) 

φ(n): The number of positive integers less than n that is relative prime to n.

**Theorem**:

+ φ(p) = p−1 for any prime number p.
+ φ(pq) = (p − 1)(q − 1) for any two distinct primes p and q.

**Proof**:

[1,pq-1] totally qp-1 number. These numbers are not relative prime to pq:
{1p, 2p ,3p, ..., (q-1)p, 1q, 2q, 3q, ..., (p-1)q}. The total number that satisfy relative prime to pq is:

φ(pq) = (pq-1) - [(q-1)+(p-1)] = (p-1)(q-1)


### Euler’s Theorem

**Euler’s Theorem**: For every integer a and n that are relatively prime,

<div>$$ a^{\phi(n)} mod n = 1.$$</div>

**Proof**: 

Define `R={1<=x<n: gcd(x,n)=1}`. Since `gcd(a,n)=1`, `gcd(x,n)=1` => `gcd(ax,n)=1`. 

Since `gcd(ax,n)=1`, `gcd(n, ax mod n)=1` (辗转相除法可证). `R={1<=x<n: gcd(x,n)=1}`(that is all num that coprime with n) and `ax mod n < n`, that is `ax mod n` is relative prime to n and is less then n, so it should be in R. 

If i!=j, then xi!=xj. Because a is coprime with n, `a * xi mod n = a * xj mod n`((a mod n) * (xi mod n) =? (a mod n) * (xj mod n)) NOT equal.

So x1x2x3...xφ(n) = ax1 * ax2 * ... * axφ(n) mod n = a^{φ(n)}x1x2x3...xφ(n) mod n.

So **a^{φ(n)} mod n = 1**

### Fermat’s Theorem

**Ferma’s Theorem**: Let p be a prime. For every integer a that is not a multiple of p,

<div>$$a^{p-1} mod p =1$$</div>

**Remark**: It is a special case of Euler's theorem.

## The RSA Public-Key Cryptosystem

**Plaintext space**: M = {0, 1} *.  
**Ciphertext space**: C = {0, 1} *.  

Choose two distinct primes p and q. Define n = pq.

**Private key**: d: 1 <= d < φ(n) with gcd(d, φ(n)) = 1  
**Public key**: (e,n) : e is the multiplicative inverse of d modulo φ(n).  
**Public-key space**: Ke = {1 <= i < φ(n) : gcd(i, φ(n)) = 1} × {n}  
**Private-key space**: Kd = {1 <= i < φ(n) : gcd(i, φ(n)) = 1}

Let 2^k < n < 2^(k+1), i.e., k = ⌊log2 n⌋. Plaintext is broken into blocks of length k.

**Encryption**: For each block M, C = M^e mod n  
**Decryption**: M = C^d mod n

### Correctness of Decryption: M = Cd mod n

gcd(M,n) = gcd(M,pq) = {1, p, q, pq} totally 4 cases.

**Proof: Case I** gcd(M,n)=1

By Euler's theorem,

	C^d mod n	= M^(ed) mod n
				= M^(uφ(n)+1) mod n				<= (ed mod φ(n)=1) = (ed = uφ(n)+1)
				= (M^(uφ(n)) mod n)(M mod n)
				= ((M^φ(n) mod n)^u)(M mod n)
				= M								<= Euler's theorem

**Proof: Case II** gcd(M, n) = p

We have M=tp, 0<t<q. So gcd(M,q)= 1. Since ed=uφ(n)+1 for some u, by Fermat’s

	(M^(uφ(n))-1) mod q	= ([M^(u(p-1))]^(q-1) -1) mod q = 0 (Fermat’s theorem)

	(M^(ed)-M) mod n	= M(M^(ed-1)-1) mod n
						= tp(M^(uφ(n))-1) mod pq
						= 0

	C^d mod n	= M^(ed) mod n
				= (M^(ed)-M) mod n + M
				= M

**Proof: Case III** gcd(M, n) = q

Similar to Case II.

**Proof: Case IV** gcd(M,n) = pq

gcd(M,n)=pq only when M=0. Then C=0. Satisfy.

### The RSA Public-Key Cryptosystem: Example

**Parameters**:

	p	q	n	φ	e	d
	5	11	55	40	7	23

**Public key**: (e,n) (7,55)
**Private key**: d 23  
**Encryption**: M = 28, C = M^e mod n = 28^7 mod 55 = 52  
**Decryption**: M = C^d mod n = 52^23 mod 55 = 28

**Other parameters**: p, q, φ(n) must be kept secret.


