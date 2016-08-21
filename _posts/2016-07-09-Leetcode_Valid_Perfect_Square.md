---
layout: post
title:  "Leetcode - 367. Valid Perfect Square"
date:   2016-07-09 12:00:00
tags:	technology
myTag:	algorithm
headimg: .jpg

---
### [367. Valid Perfect Square](https://leetcode.com/problems/valid-perfect-square/)

Given a positive integer num, write a function which returns True if num is a perfect square else False.

Note: Do not use any built-in library function such as sqrt.

Example 1:

	Input: 16
	Returns: True

Example 2:

	Input: 14
	Returns: False

### 1. Analyse

It is a typical `Binary Search` problem. But there is 1 point we should be careful. Let see the Wrong code.

### 2. Wrong Code

	class Solution {
	public:
	    bool isPerfectSquare(int num) {
	        if( num < 0 )   return false;
	        int left = 0, right = num;
	        while( left <= right ){
	            int mid = left + (right-left)/2;
	            if( mid*mid < num ){
	                left = mid+1;
	            else if( mid*mid > num )
	                right = mid-1;
	            else 
	                return true;
	        }
	        return false;
	    }
	};


The above code seems to be correct, but when the test case is 2147483647, it goes wrong. The reason is that after the first loop calculation `mid = 1073741823`. In the second loop, `mid*mid` is overflow. It is weird, because I remember that an `int` multiple an `int` will automatically extend to be a `long` type, but I'm wrong. The type of mutiple result will be the longest type of multiplier. So an `int` multiple an `int` will get an `int` instead of a `long`.

### 3. AC Code

(17 Lines)

	class Solution {
	public:
	    bool isPerfectSquare(int num) {
	        if( num < 0 )   return false;
	        int left = 0, right = num;
	        while( left <= right ){
	            long mid = left + (right-left)/2;
	            if( mid*mid < num ){
	                left = mid+1;
	            else if( mid*mid > num )
	                right = mid-1;
	            else 
	                return true;
	        }   
	        return false;
		}	
	};

### Reference

1. [c语言的自动类型转换. hoys](http://www.cnblogs.com/hoys/archive/2012/04/09/2438718.html)
