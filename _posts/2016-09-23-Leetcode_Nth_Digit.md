---
layout: post
title:  "Leetcode - 400. Nth Digit"
date:   2016-09-23 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [400. Nth Digit](https://leetcode.com/problems/nth-digit/)

Find the nth digit of the infinite integer sequence 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ...

Note:
n is positive and will fit within the range of a 32-bit signed integer (n < 2^31).

Example 1:

	Input:
	3
	
	Output:
	3

Example 2:

	Input:
	11
	
	Output:
	0

Explanation:
The 11th digit of the sequence 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, ... is a 0, which is part of the number 10.

### 1. Analyse

One digit numbers range \[1,9\](total 9). Two digit numbers range \[10,99\](total 90). Three digit numbers range \[100,999\](total 900).

### 2. AC Code

(18 Lines)

	class Solution {
	public: 
	    int findNthDigit(int n) {
	        long num = 9;
	        int digit = 1;
	        while( n > digit*num ){
	            n -= digit*num;
	            digit++;
	            num *= 10;
	        }
	        
	        long target = 1;
	        for(int i=1; i<digit; i++)
	            target *= 10;
	        target += (n-1)/digit;
	        return 0 == n%digit ? to_string(target)[digit-1]-'0' : to_string(target)[n%digit-1]-'0';
	    }   
	};
