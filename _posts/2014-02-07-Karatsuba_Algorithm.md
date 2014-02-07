---
layout: post
title:  "Karatsuba Algorithm"
date:   2014-02-07 12:00:00
tags:	tech
myTag:	

---

# Karatsuba Algorithm

----------------------

今天看到G家有一道面试题目是`快速乘法(Karatsuba)算法是什么?`。因为不知道Karatsuba究竟是什么，所以就百度了一下。其实原理很简单，但是也很有效。(ps:百度百科现在是做得越来越好了哦)

---------------------------------------------------------------------------

### 算法概述

Karatsuba乘法是一种快速乘法算法。由Anatolii Alexeevitch Karatsuba于1960年提出，并于1962年发表。一般我们做高精度乘法，普通算法的复杂度为`n^2`，而Karatsuba算法的复杂度为`3n^log3 ≈ 3n^1.585`。

---------------------------------------------------------------------------

### 算法描述

首先有两个大数`x`和`y`。先将x和y分别拆分成两部分x1，x0，y1，y0。

	x = x1 * 10^m + x0
	y = y1 * 10^m + y0

其中`m`为正整数，m<n，且x0，y0小于10^m。

那么有:

	x * y = (x1 * 10^m + x0) * (y1 * 10^m + y0)
	x * y = z2 * 10^2m + z1 * 10^m + z0

其中

	z2 = x1 * y1
	z1 = x1 * y0 + x0 * y1
	z0 = x0 * y0

此步骤需要共4次乘法，但由Karatsuba改进后仅需要3次乘法。因为

	z1 = x1 * y0 + x0 * y1
	z1 = (x1+x0)*(y1+y0) - x1*y1 - x0*y0
	z1 = (x1+x0)*(y1+y0) - z2 - z0


### 举个例子

	设x=12345, y=6789, 令m=3。

	12345 = 12 * 1000 + 345
	6789 = 6 * 1000 + 789

	z2 = 12 * 6 = 72
	z0 = 345 * 789 = 272205
	z1 = (12+6)*(345+789) - z2 - z0 = 11538

	x*y = 72 * 1000^2 + 11538 * 1000 + 272205 = 83810205

---------------------------------------------------------------------------

### 编程实现

伪代码描述

	procedure karatsuba(num1, num2)
		if (num1 < 10) or (num2 < 10)   
			return num1*num2  
		
		/* calculates the size of the numbers */ 
		m = max(size(num1), size(num2))  
		low1, low2 = lower half of num1, num2  
		high1, high2 = higher half of num1, num2  
		
		/* 3 calls made to numbers approximately half the size */ 
		z0 = karatsuba(low1,low2)  
	    z1 = karatsuba((low1+high1),(low2+high2))  
	    z2 = karatsuba(high1,high2)  
	    
		return (z2*10^(m))+((z1-z2-z0)*10^(m/2))+(z0)
