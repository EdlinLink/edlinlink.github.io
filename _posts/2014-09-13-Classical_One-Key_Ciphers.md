---
layout: post
title:  "[CSIT 5710] Classical One-Key Ciphers"
date:   2014-09-13 12:00:00
tags:	tech
myTag:	security

---

# Classical One-Key Ciphers

-----------------------------

### Classical One-key Cipher or Cryptosystem

<div>

Typically there is a 5-tuple \((M, C, K, E_k, D_k)\) in a classical one-key cipher or crytosystem.

\(M\): plaintext space;  
\(C\): ciphertext space;  
\(K\): key space;  
\(E_k\): encryption transformation  
\(D_k\): decryption transformation  

</div>

### Attacks on Classical Cryptosystems

1. Ciphertext-only attack: determine **decryption transformation** or **key** or **plaintext** from the ***ciphertext***.
2. Known-plaintext attack: determine **decryption transformation** or **key** from a ***ciphertext-plaintext pair (c,m)***.

### Security Requirements

1. The security should depend on the confidentiality of the **key**, so it is assumed that the algorithms \(E_k\) and \(D_k\) are known to a cryptanalyst. (假定**加密**和**解密**方法是已知的，保密的只是**key**)
2. It should be computationally infeasible for a cryptanalyst to determine the plaintext (\m\), given a ciphertext \(c\). (破解者无法通过计算从**秘文**知道**明文**)
3. It should be computationally infeasible for a cryptanalyst to systematically determine the decryption transformation \(D_k\) or key \(k\) from intercepted ciphertext c, even if the corresponding plaintext m is known. (就算对应的**明文**被知道了，破解者也无法通过计算从**秘文**知道**解密**方法或者**key**)

---------------------------------------------------------------------------

### Permutations of Zd

Let Zd denote the set of integers 0 through d−1. A permutation f of Zd is a one-to-one mapping from Zd to itself.

Example:

	Let d = 4 and define f by:
	i	:	0	1	2	3
	f(i):	2	0	3	1

Then f is a permutation of Z4.


### Transposition Ciphers

Example:

	Let d=4 and define f by:
	i	:	0	1	2	3
	f(i):	2	0	3	1

The message RENAISSANCES s broken into groups of 4 letters and encrypted into

	position	0123 0123 0123
	m		  = RENA ISSA NCES
	Ek(m)	  = NRAE SIAS ENSC

Remark: Transposition ciphers are **not** secure.

---------------------------------------------------------------------------

### Simple Substitution Ciphers
	
Let A=B be the English alphabet. We identify letters with digits:

	a	b	c	...	y	z
	0	1	2	...	24	26

Take any (k0, k1) with gcd(k0,26) = 1 and 0<=k0<=25, define the 1-to-1 mapping f by

$$f(a) = (ak_0 + k_1) mod 26.$$

It is called the [affine cipher](http://en.wikipedia.org/wiki/Affine_cipher), where the key k=(k0,k1) or k=f.

If (k0,k1) = (1,3), it is the **Caesar cipher** [(恺撒加密)](http://baike.baidu.com/view/1236600.htm?fr=aladdin).

+ **Question**: Why should gcd(k0,26)=1 ?

+ **Answer**: "Anyone who knows please tell me."	

### Frequency Distribution of English Letters

	A		B		C		D		E		F		G
	8.0		1.5		3.0		4.0		13.0	2.0		1.5

	H		I		J		K		L		M		N
	6.0		6.5		0.5		0.5		3.5		3.0		7.0

	O		P		Q		R		S		T		U
	8.0		2.0		0.2		6.5		6.0		9.0		3.0 
	
	V		W		X		Y		Z 
	1.0		1.5		0.5		2.0		0.2

The uneven distribution of letters makes it easy to break simple substitution ciphers.

### Security of Simple Substitution Ciphers

+ **Language redundancy**: It is needed in natural (human) languages for error detection and correction. (在一句话中，我知道你说错了一个词语。我之所以知道是因为这句话其他词语其实对这个词有作用，也表达了一些类似的意思) (English redundancy > Chinese redundancy, easier to learn English than Chinese)

+ **Conclusion**: For English, about 28 letters are needed to “break” a simple substitution cipher.

+ **Ciphertext-only attack**: 计算各个秘文字母的频率，尝试将秘文字母与明文字母对比。

+ **Known-plaintext attack**: 知道两组明文-秘文对(m1,c1)和(m2,c2)，则可解方程

		c1 = (m1k0 + k1) mod 26
		c2 = (m2k0 + k1) mod 26


