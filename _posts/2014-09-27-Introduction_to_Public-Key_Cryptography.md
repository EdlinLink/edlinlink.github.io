---
layout: post
title:  "[CSIT 5710] Lecture 08: Introduction to Public-Key Cryptography"
date:   2014-09-27 13:00:00
tags:	tech
myTag:	security

---

# Lecture 08: Introduction to Public-Key Cryptography

---------------------------------------------------------------------------

1. Introduce the idea of public-key cryptography. 
2. Mention three applications of public-key crypto-graphy. 

## Public-key Cryptography

### Disadvantages of One-Key Cryptography

**One-key cryptosystem**: (M, C, K, Ek, Dk), where the encryption and decryption keys are the same.

+ The sender and receiver must share the same secret key. *Key distribution is a must*.
+ If 1000 people want to communicate (two and two, in all possible ways), each must keep 999 secret keys, and the system requires a total of 999·1000/2 = 499500 secret keys. This makes key management difficult.

### Two-key Cryptosystems

A six-tuple (M, C, Ke, Kd, Eke , Dkd ), where

+ M, C, Ke,Kd are respectively the plaintext space, ciphertext space, encryption key space, and decryption key space;
+ ke ∈ Ke and kd ∈ Kd are corresponding encryption and decryption keys respectively;
+ Eke	and Dkd	are the encryption and decryption transformations, and

<div> $$D_{k_d}(E_{k_e}(m)) = m$$ </div>

for all m ∈ M (unique and correct decryption).

**Remark**: "ke" and "kd" are not the same even "Ke" and "Kd" are the same.

### The Idea of Public-Key Cryptography

Suppose that I have a two-key cryptosystem (M, C, Ke, Kd, Eke , Dkd ). Suppose further that given the encryption key *ke*, the encryption algorithm and the decryption algorithm,

**C1**: it is “computationally infeasible” to derive the decryption key kd from the given encryption key ke; and  
**C2**: it is “computationally infeasible” to derive the plaintext m if the corresponding ciphertext c is known.

I publish *ke* together with the encryption algorithm in a public directory, in order for anybody else to encrypt a message and send it to me.

## Applications in Encryption

Given a public-key cryptosystem (M, C, Ke, Kd, Eke , Dkd ):

+ Alice generates a key pair (ke(A),kd(A)), keeps the decryption key kd(A) confidential, and publishes the encryption key kd(A) and the encryption algorithm in a public directory.
+ If Bob wants to send a message m to Alice, he finds Alice's encryption
key ke(A) and the encryption algorithm in the public directory, encrypts the message to get c = Eke(A)(m), and sends c to A.
+ After receiving c, Alice uses her decryption key and computes

<div>$$D_{k_d^{(A)}}(c) = D_{k_d^{(A)}}(E_{k_e^{(A)}}(m)) = m$$</div>


## Applications in Digital Signature

Suppose that the system has the further property

<div>$$E_{k_e}(D_{k_d}(x)) = x,$$</div>

for all x ∈ C. 

Then we can use such a system to **sign** messages.

+ To sign a message *m*, the sender applies a public hash function *f* to *m* obtaining *f(m)*, which is called the **message digest**.
+ He then uses his private key to sign on the message digest, obtaining *Dkd(f(m))*. Then he sends the data *m||Dkd(f(m))* to the receiver.

**Question**: Why do we need a hash function here?

**Answer**: To make the message digest fix length.

**Checking the validity of signature**

+ To check the validity of the sender's signature, the receiver breaks the received message c into two parts *m'||c2*, where *c2* has a fixed length (i.e., the length of the signature). Then he uses the sender’s public key to obtain *Eke(c2)*.
+ He computes *f(m)*, (the hash function is public).
+ Finally, he compares *f(m)* with *Eke(c2)*. If they match, he accepts *m'||c2* as a valid message with signature from the sender. Otherwise he rejects it.

## Applications in Key Exchange

**Session key**: Two parties want to communicate using a one-key cipher for encryption. They need a session key for each session of communication.

**Session key exchange/distribution**

+ Two sides cooperate to exchange a session key.
+ Several different approaches are possible, involving the private key(s) of
one or both parties.

## Applications of Public-Key Cryptography

**Three types of applications**: Encryption, digital signature, key exchange
