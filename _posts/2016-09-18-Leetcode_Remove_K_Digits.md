---
layout: post
title:  "Leetcode - 402. Remove K Digits"
date:   2016-09-18 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [402. Remove K Digits](https://leetcode.com/problems/remove-k-digits/)

Given a non-negative integer num represented as a string, remove k digits from the number so that the new number is the smallest possible.

Note:
+ The length of num is less than 105 and will be ≥ k.
+ The given num does not contain any leading zero.

Example 1:

	Input: num = "1432219", k = 3
	Output: "1219"
	Explanation: Remove the three digits 4, 3, and 2 to form the new number 1219 which is the smallest.

Example 2:

	Input: num = "10200", k = 1
	Output: "200"
	Explanation: Remove the leading 1 and the number is 200. Note that the output must not contain leading zeroes.

Example 3:

	Input: num = "10", k = 2
	Output: "0"
	Explanation: Remove all the digits from the number and it is left with nothing which is 0.

### 1. Analyse

Let's see some example. If given `4321`,

	4321	k=1 -->	321
	4321	k=2 --> 21
	4321	k=3 --> 1

If given `2341`,
	
	2341	k=1 --> 231
	2341	k=2 --> 21
	2341	k=3 --> 1

From the previous examples, if the number is decreasing, we remove the first one; if the number is increasing, we remove the last one.

### 2. AC Code

(25 Lines)

	class Solution {
	public:
	    string removeKdigits(string num, int k) {
			num += '0';
			if( k >= num.size()-1 )	return string("0");        
			vector<char> vec; 
			for( int i=0; i<num.size(); i++ ){
				if( vec.empty() || num[i] >= vec.back() )
					vec.push_back( num[i] );
				else{
					while( !vec.empty() && num[i] < vec.back() && k ){
						k--;
						vec.pop_back();
					}
					vec.push_back( num[i] );
				}
			}
			string ans;
			for( int i=0; i<vec.size()-1; i++ ){
				if( !ans.empty() || vec[i]!='0' )
					ans += vec[i];
			}
			return ans.empty() ? "0" : ans;
	    }
	};
	
### 3. Concise AC Solution

(22 Lines)

	class Solution {
	public:
	    string removeKdigits(string num, int k) {
			if( k >= num.size() )	return string("0");        

			num += '0';
			vector<char> vec; 
			for( int i=0; i<num.size(); i++ ){
				while( !vec.empty() && num[i] < vec.back() && k ){
					k--;
					vec.pop_back();
				}
				vec.push_back( num[i] );
			}
			string ans;
			for( int i=0; i<vec.size()-1; i++ ){
				if( !ans.empty() || vec[i]!='0' )
					ans += vec[i];
			}
			return ans.empty() ? "0" : ans;
	    }
	};



