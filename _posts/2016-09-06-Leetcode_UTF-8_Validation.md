---
layout: post
title:  "Leetcode - 393. UTF-8 Validation"
date:   2016-09-06 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [393. UTF-8 Validation](https://leetcode.com/problems/utf-8-validation/)

A character in UTF8 can be from 1 to 4 bytes long, subjected to the following rules:

1. For 1-byte character, the first bit is a 0, followed by its unicode code.
2. For n-bytes character, the first n-bits are all one's, the n+1 bit is 0, followed by n-1 bytes with most significant 2 bits being 10.

This is how the UTF-8 encoding would work:

	Char. number range  |        UTF-8 octet sequence
	   (hexadecimal)    |              (binary)
	--------------------+---------------------------------------------
	0000 0000-0000 007F | 0xxxxxxx
	0000 0080-0000 07FF | 110xxxxx 10xxxxxx
	0000 0800-0000 FFFF | 1110xxxx 10xxxxxx 10xxxxxx
	0001 0000-0010 FFFF | 11110xxx 10xxxxxx 10xxxxxx 10xxxxxx

Given an array of integers representing the data, return whether it is a valid utf-8 encoding.

**Note:**

The input is an array of integers. Only the least significant 8 bits of each integer is used to store the data. This means each integer represents only 1 byte of data.

**Example 1:**

	data = [197, 130, 1], which represents the octet sequence: 11000101 10000010 00000001.
	
	Return true.
	It is a valid utf-8 encoding for a 2-bytes character followed by a 1-byte character.

**Example 2:**

	data = [235, 140, 4], which represented the octet sequence: 11101011 10001100 00000100.
	
	Return false.
	The first 3 bits are all one's and the 4th bit is 0 means it is a 3-bytes character.
	The next byte is a continuation byte which starts with 10 and that's correct.
	But the second continuation byte does not start with 10, so it is invalid.

### 1. Analyse

	`AND` the mask `0xxxxxxx`, `110xxxxx`, `1110xxxx` and `11110xxx`. 

	0xxxxxxx	[00000000,01111111]-->[0,127]
	110xxxxx	[11000000,11011111]-->[192,223]
	1110xxxx	[11100000,11101111]-->[224,239]
	11110xxx	[11110000,11110111]-->[240,247]

	10xxxxxx	[10000000,10111111]-->[128,191]
### 2. AC Code

class Solution {
public:
    bool validUtf8(vector<int>& data) {

 		for( int i=0; i<data.size(); i++){
			int follow_bys = 0;
			if( 0 <= data[i] && data[i] <= 127 )		follow_bys = 0;
			else if( 192 <= data[i] && data[i] <= 223 )	follow_bys = 1;
			else if( 224 <= data[i] && data[i] <= 239 )	follow_bys = 2;
			else if( 240 <= data[i] && data[i] <= 247 )	follow_bys = 3;
			else										return false;

			if(i+follow_bys >= data.size())	return false;

			for( int j=1; j<=follow_bys; j++ ){
				if( ! (128 <= data[i+j] && data[i+j] <= 191) )
					return false;
			}

			i += follow_bys;
		}       
		return true;
    }
};

