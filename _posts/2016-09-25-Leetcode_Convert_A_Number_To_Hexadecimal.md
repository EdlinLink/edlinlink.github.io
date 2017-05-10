---
layout: post
title:  "Leetcode - 405. Convert a Number to Hexadecimal"
date:   2016-09-23 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [405. Convert a Number to Hexadecimal](https://leetcode.com/problems/convert-a-number-to-hexadecimal/)

Given an integer, write an algorithm to convert it to hexadecimal. For negative integer, two’s complement method is used.

Note:

+ All letters in hexadecimal (a-f) must be in lowercase.
+ The hexadecimal string must not contain extra leading 0s. If the number is zero, it is represented by a single zero character '0'; otherwise, the first character in the hexadecimal string will not be the zero character.
+ The given number is guaranteed to fit within the range of a 32-bit signed integer.
+ You must not use any method provided by the library which converts/formats the number to hex directly.

Example 1:

	Input:
	26

	Output:
	"1a"

Example 2:

	Input:
	-1

	Output:
	"ffffffff"

### 1. Analyse

Convert the number to binary expression and then convert to hexadecimal.

### 2. AC Code

(16 Lines)

    class Solution {
    public:
        string toHex(int num) {
            map<int,string> mp; 
            mp[0]="0",  mp[1]="1",  mp[2]="2",  mp[3]="3",  mp[4]="4",  mp[5]="5",  mp[6]="6",  mp[7]="7",  
            mp[8]="8",  mp[9]="9",  mp[10]="a", mp[11]="b", mp[12]="c", mp[13]="d", mp[14]="e", mp[15]="f";

            string ret;
            for( int i=0; i<8 && num; i++ ){
                ret = mp[(unsigned int)num%16] + ret;     
                num >>= 4;
            }
                
            return ret.empty() ? "0" : ret;
        }   
    };

There is a trick here.

	//In C++
	-1 % 16 = -1
	-1 / 16 = 0

	//In Python
	-1 % 16 = 15
	-1 / 16 = -1

So I can just use bit operation `>>` rather than `/`.
