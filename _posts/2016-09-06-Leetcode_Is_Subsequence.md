---
layout: post
title:  "Leetcode - 392. Is Subsequence"
date:   2016-09-06 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [392. Is Subsequence](https://leetcode.com/problems/is-subsequence/)

Given a string s and a string t, check if s is subsequence of t.

You may assume that there is only lower case English letters in both s and t. t is potentially a very long (length ~= 500,000) string, and s is a short string (<=100).

A subsequence of a string is a new string which is formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (ie, "ace" is a subsequence of "abcde" while "aec" is not).

Example 1:
s = "abc", t = "ahbgdc"

Return true.

Example 2:
s = "axc", t = "ahbgdc"

Return false.

### 1. Analyse

A `for` loop ?!

### 2. AC Code

(12 Lines) 

	class Solution {
	public:
	    bool isSubsequence(string s, string t) {
			int index = 0;
	 		for( int i=0; i<s.size(); i++ ){
				index = t.find( s[i], index )+1;
				if( 0 == index )
					return false;
			}       
			return true;
	    }
	};
	
The only trick here is `string::find` will find the string from index argument2. We need to add 1 when using the index next time.
