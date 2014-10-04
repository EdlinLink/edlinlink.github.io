---
layout: post
title:  "[CSIT 5710] Lecture 10: The ElGamal Public-Key Cryptosystem"
date:   2014-10-04 12:00:00
tags:	tech
myTag:	security

---

# Lecture 10: The ElGamal Public-Key Cryptosystem

---------------------------------------------------------------------------

1. The discrete logarithm problem. 
2. The ElGamal public-key cryptosystem. 

## Discrete Logarithm Problem

**The discrete logarithm problem** : Let p be a prime, and let α be a primitive root of p. The discrete logarithm problem is to find logα(a) for any 1 <= a <= p−1, which is defined to be the unique integer 0 <= i <= p−2 such that

<div>$$a = \alpha ^ i (mod p)$$</div>

**Comment**: No polynomial-time algorithm is known for this problem (except for certain special primes p).  
**Comment**: If p has 130 or more digits, the DLP is computationally infeasible to solve in general.

**Private key**: kd = u. secret number u in **Z**(p-1)  
**Public key***: ke = (p, α, β). where β = α^u mod p. p should be large prime.


## The ElGamal System

**Encryption**: For any public key ke = (p, α, β), and for a (secret) random number v ∈ **Z**p−1,

<div>$$E_{k_e}(x,v) = (y1, y2),$$ </div>

where

<div>$$ya=\alpha ^ v mod p, y2 = x\beta ^ v mod p.$$</div>

**Decryption**: For any (y1, y2) ∈ Zp * × Zp *,

<div>$$D_{k_d}(y1,y2) = y2(y1^{k_d})^-1 mod p$$</div>

**Remark**: Encryption is nondeterministic, and has data expansion.

**Proof**: Note that β = α^u mod p and

<div>$$ya=\alpha ^ v mod p, y2 = x\beta ^ v mod p.$$</div>

We have

	Dkd(y1,y2)	= y2(y1^u)^-1 mod p			<=	-1 represent inverse
				= xβ^v([α^v)^u)^-1 mod p
				= x[α^(uv)][α^(uv)]^-1 mod p
				= x mod p
				= x

**Remark**: For decryption, the receiver need not know the secret random number v!!!  
**Remark**: The system is not deterministic, since the ciphertext depends on both the plaintext x and the random number v chosen by Alice, the sender.  
**Remark**: The ElGamal system cannot be used for signing messages, as M ≠	C.

### Week Keys in the ElGamal Cryptosystem

The following two pairs of keys are weak (in fact, cannot be used):

+ ke =(p,α,α),kd =u=1.

	Once ke is published, kd is easily seen to be 1. Hence x = y2y1^−1.

+ ke =(p,α,1),kd =u=0.

	Once ke is published, kd is easily seen to be 0. Hence x = y2.
	
