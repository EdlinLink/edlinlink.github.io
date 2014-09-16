---
layout: post
title:  "[CSIT 5710] Basic Design Ideas of One-Key Ciphers"
date:   2014-09-14 12:00:00
tags:	tech
myTag:	security

---

# Basic Design Ideas of One-Key Ciphers

---------------------------------------------------------------------------

## Part 1: Linear and Nonlinear Functions

### Abelian groups

**Abelian group** [(阿贝尔群 - Baidu)](http://zh.wikipedia.org/wiki/%E9%98%BF%E8%B4%9D%E5%B0%94%E7%BE%A4) *is a group in which the result of applying the group operation to two group elements does not depend on their order* [(Abelian group - wiki)](http://en.wikipedia.org/wiki/Abelian_group).

If A is a finite set, (A, +) is called a finite Abelian group. All we consider only finite Abelian groups.

1. **Example of Abelian groups**: (Zp,⊕p) is a finite Abelian group with p elements, where p is any prime and Zp = {0,1,2,...,p−1}.
	+ **Question**: What is the identity element of (Zp,⊕p)?
	+ **Answer**: np (n is an integer).

2. **Example of Abelian groups**: (Z\*p,⊗p) is a finite Abelian group with p−1 elements, where p is any prime and Z\*p = {1,2,··· ,p−1}.
	+ **Question**: What is the identity element of (Z\*p,⊗p)?
	+ **Answer**: 1+np (n is an integer).

**Remark**: The finite field Zp has two Abelian groups: the additive group (Zp, ⊕p), and multiplicative group (Z\*p, ⊗p).

-------------------------------

### Linear and Affine Functions

A function f from an Abelian group (A, +) to an Abelian group (B, +) is called **linear** if and only if f(x + y) = f(x) + f(y) for all x, y ∈ A.

A function g : A → B is **affine** if and only if g = f + b for a linear function f : A → B and a constant b ∈ B.

-------------------------------

### Nonlinear Functions

1. **The First Nonlinearity Measure** 

	The **Hamming distance** between two functions f and g from Abelian group (A, +) to Abelian group (B, +), denoted by d(f, g), is defined to be

	<div>$$ d(f,g) = |{x \in A| f(x)-g(x) \ne 0}|. $$</div>

	The **first measure of nonlinearity** is defined by:

	<div>$$ N_f = \displaystyle\min_{l \in L}d(f,l), $$</div>

	where L denotes the set of all affine functions from (A,+) to (B,+). 

	**Conclusion 1**: A function from (A,+) to (B,+) is affine iff Nf = 0. (从定义来)  
	**Conclusion 2**: The larger the value of Nf, the higher the nonlinearity of f.

	实际这种方法是拟合一个线性函数.

	
2. **The Second Nonlinearity Measure**

	The second nonlinearity measure is defined by:

	<div>$$ P_f = \displaystyle max_{0\ne a\in A} \displaystyle max_{b \in B} \frac{|{x\in A:f(x+a)-f(x)=b}|}{|A|}$$</div>

	**Conclusion 1**: If f is affine, then Pf = 1.

	**Question**: If pf = 1, must f be affine?

	**Answer**: No. eg: f((x1,x2,x3,x4)) = x1 + x2 + x3 + x4 + x1x2x3.

	**Conclusion 2**: The smaller the value of Pf, the higher the corresponding nonlinearity of f.


3. <div> Example: The folling function froms \(Z_2^4\) to \(Z_2\) are nonlinear: </div>

<div> $$f(x_1, x_2, x_3, x_4) = x_1 + x_2 + x_3 + x_4 + x_1x_2x_3x_4$$ 

 $$g(x_1, x_2, x_3, x_4) = x_1 + x_2 + x_3 + x_4 + x_1x_2x_3$$ </div>

+ **Question** :Which of them has higher “nonlinearity”?
+ **Answer**: 

	1. Nf = min d(f,l) = |f-(x1+x2+x3+x4)| = |x1x2x3x4| = 1;
	2. Ng = min d(g,l) = |g-(x1+x2+x3+x4)| = |x1x2x3| = 2;
	3. Pf = |a1+a1x2x3x4=b|/|A|, (a=(1,0,0,0), b=1) = 14/16 = 7/8;
	4. pg = |a4=b|/|A|, (a=(0,0,0,1), b=1) = 16/16=1;


## Part 2: Shannon’s Diffusion and Confusion

+ **Diffusion**: Each plaintext block bit or key bit affects many bits of the ciphertext block. (描述作用的情况)

+ **Confusion**: Each bit of the ciphertext block has highly nonlinear relations with the plaintext block bits and the key bits. (描述非线性的情况)


## Part 3: An Important Design Paradigm

### 1. The Iterative Design Paradigm

<div>$$ E_k(m) = f_k_16(f_k_15(...k_k_2(f_k_1(m))...))$$</div>

1. they have good diffusion and confusion with respect to the secret key bits and message block bits, and
2. they are fast in software and hardware

**Remark**: Most ciphers are designed with this approach.
