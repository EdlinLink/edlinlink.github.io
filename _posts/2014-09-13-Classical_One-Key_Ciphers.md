---
layout: post
title:  "Classical One-Key Ciphers"
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

### Permutations of \(Z_\d)

Let (\Z_d\) denote the set of integers 0 through \(d−1\). A permutation \(f\) of \(Z_d\) is a one-to-one mapping from \(Z_d\) to itself.

Example:

	Let d = 4 and define f by:
	i	:	0	1	2	3
	f(i):	2	0	3	1

Then \(f\) is a permutation of \(Z_4\).


