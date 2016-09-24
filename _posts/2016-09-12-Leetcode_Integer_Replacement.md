---
layout: post
title:  "Leetcode - 397. Integer Replacement"
date:   2016-09-12 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [397. Integer Replacement](https://leetcode.com/problems/integer-replacement/)

Given a positive integer n and you can do operations as follow:

If n is even, replace n with n/2.
If n is odd, you can replace n with either n + 1 or n - 1.
What is the minimum number of replacements needed for n to become 1?

Example 1:

	Input:
	8
	
	Output:
	3
	
	Explanation:
	8 -> 4 -> 2 -> 1

Example 2:

	Input:
	7
	
	Output:
	4
	
	Explanation:
	7 -> 8 -> 4 -> 2 -> 1
	or
	7 -> 6 -> 3 -> 2 -> 1

### 1. Analyse

Recursion Algorithm.

### 2. AC Code

class Solution {
public:
    int integerReplacement(int n) {
        return (int)integerReplacement((long)n);
    }
        
    long integerReplacement(long n){
        if( n <= 3 )    return (n-1);
        else            return ( n%2 ? min(integerReplacement(n+1),integerReplacement(n-1))+1 : integerReplacement(n/2)+1 );
    }   
};

### Reference

1. [Leetcode 397. Integer Replacement 整数替换 解题报告](http://blog.csdn.net/MebiuW/article/details/52511829)
