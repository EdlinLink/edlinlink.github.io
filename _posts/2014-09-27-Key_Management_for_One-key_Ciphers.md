---
layout: post
title:  "[CSIT 5710] Lecture 07: Key Management for One-key Ciphers"
date:   2014-09-27 12:00:00
tags:	tech
myTag:	security

---

# Lecture 07: Key Management for One-key Ciphers

---------------------------------------------------------------------------

1. A key distribution protocol with a key distribution center. 
2. The Diffie-Hellman key exchange protocol.

## A Key Distribution Protocol

**Parties involved**: A key distribution center (KDC), a group of people to communicate with each other.  

**Requirements**: Whenever A wants to communicate with B, the KDC should generate a temporary key (called **session key**) and distribute it to A and B. Both confidentiality and authenticity must be achieved.

**Remark**: The session key (temporary key) is established only for this communications between A and B.

**Building blocks needed**:

+ The KDC and all parties involved in this communication system use a one-key block cipher.
+ The KDC and each party A share a secret key ka, which is called a **master key**.

**Remark**: The master keys are used to protect the sessions keys when they are distributed.


**Pictorial description of use of the key hierarchy**:

	+-------------------+
	|	 master keys	|	- non-cryptographic protection
	+-------------------+	use for a long time
			  |				share between one member and the KDC
			  v
	+-------------------+
	|	 session keys	|	- crptographic protection
	+-------------------+	use for only a session
			  |				share between two members
			  v
	+-------------------+
	|		 data		|	- cryptographic protection
	+-------------------+	

--------------------

### A Key Distribution Protocal

	
		+->	(1) request||N1	--> +-------+
		|						|  KDC	|
		|			+---------- +-------+
		|			|
		|			|
		|	(2) Eka[k||request||N1||Ekb(k||IDa)]		
		|			|
		|			v
	+---+---------------+ ---- (3) Ekb[k||IDa] --->	+---------------+	
	|	 Initiator A	| <--- (4) Ek[N2||IDb] ----	|  Responder B	|
	+-------------------+ ---- (5) Ek[N2] -------->	+---------------+

### Explaining the Key Distribution Protocol

+ The nonce may be a timestamp, a counter, or a random number. The minimum requirement is that it differs with each request. Also it should be hard for an opponent to guess it. So random number is a good choice.

+ When A receives (2), A can verify that its original request was not altered before reception by the KDC. Because of the nonce, that is not a reply of some previous request. The message (2) also includes two items intended for B: the one-time session key k, and an identifier of A (i.e., its network address), IDa.

+ After Step (3), a session key has been securely delivered to A and B. They may begin their protected exchange.

+ Steps (4) and (5) assure B that the original message received in Step (3) was not a replay of an earlier one by a third person.
+ Steps (4), (5) and (3) are for authentication.


## Diffie-Hellman Key Exchange Protocal

### Discrete logarithms

**Primitive roots**: Let *p* be a prime. An integer *α* is called a **primitive root** of *p* if each nonzero element *a∈***F***p* can be uniquely expressed as

<div> $$ a = \alpha^i mod p$$ </div>

for some integer *i*, where 0 <= *i* <= *p*-2.

**Discrete logarithm**: The exponent i is referred to as the **discrete logarithm**, or **index**, of *a* for the base *α*, and is denoted logα(a) or indα(a).

Given α, i, p, it is easy to compute a;  
Given a, α, p, it is hard to compute i.

**Example**: 2 is a primitive root of the prime 11. Also we have log2(9)=6.

	i	|	0	1	2	3	4	5	6	7	8	9
	2^i	|	1	2	4	8	5	10	9	7	3	6

2^i mod p generate 1~p-1. (0 <= i <= p-2)

**Theorem**: Every prime p has at least one primitive root.

----------------------------

### Diffie-Hellman Key Exchange Protocal

		User A					User B
	+-----------------------+	+-----------------------+
	|	 Generate random	|	|	Generate random		|
	|	  Xa < p			|Ya |	 Xb < p				|
	|						|-->|						|
	|	 Calculate			|	|	Calculate			|
	|     Ya = α^Xa mod p	|	|	 Yb = α^Xb mod p	|
	|						|<--|						|
	|    Calculate			| Yb|	Calculate			|
	|     k = (Yb)^Xa mod p	|	|	 k = (Ya)^Xb mod p	|
	+-----------------------+	+-----------------------+	

"k" are equal. Because they are computing α^(XaXb) mod p.

+ It is for two users to exchange a key securely that can then be used for subsequent encryption of message.
+ k = α^(XaXb) mod p. Also p and α are publicly known. But Xa and Xb must be kept secret.
+ The security with respect to passive attacks is based on the belief that solving the discrete logarithm problem is hard in general. It is vulnerable to an active attack if an adversary has control over the communication cannel.
