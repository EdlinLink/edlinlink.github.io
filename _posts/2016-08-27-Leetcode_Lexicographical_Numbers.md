---
layout: post
title:  "Leetcode - 386. Lexicographical Numbers"
date:   2016-08-27 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

# [386. Lexicographical Numbers](https://leetcode.com/problems/lexicographical-numbers/)

Given an integer n, return 1 - n in lexicographical order.

For example, given 13, return: [1,10,11,12,13,2,3,4,5,6,7,8,9].

Please optimize your algorithm to use less time and space. The input size may be as large as 5,000,000.

### 1. Analyse 

The naive solution maybe add the number into a vector from 1 to n. But the problem says the size may be very large. That is to say we must know what is the element in the exactly position (because moving element in vector cost time).

If we increse the `n`, the process will be:

	1 2 3 4 5 6 7 8 9
	1 (10 11 12 13 14 15 16 17 18 19) 2 3 4 5 6 7 8 9 
	1 (10-19) 2 (20-29) 3 4 5 6 7 8 9
	1 (10-19) 2 (20-29) 3 (30-39) 4 (40-49) 5 (50-59) 6 (60-69) 7 (70-79) 8 (80-89) 9 (90-99)
	1 (10 100-109 11 110-119 12 ...) 2 (20-29) 3 (30-39) ...

For a number X, if `X * 10` is smaller than `n`, then 10X is the next element, else X+1 will be the next element. What if the `X % 10 == 9`? Then `(X+1)/10` will be the next element.

### 2. AC Code

class Solution {
public:
	vector<int> lexicalOrder(int n) {
		vector<int> ans(n, 1);
		for( int i=1; i<n; i++){
			int element = 0;
			if( ans[i-1] * 10 <= n )
				ans[i] = ans[i-1] * 10;
			else if( ans[i-1] % 10 == 9 ){
				ans[i] = ans[i-1] + 1;
				while( ans[i] % 10 == 0 )
					ans[i] /= 10;
			}
			else if( ans[i-1] == n )
				ans[i] = ans[i-1] / 10 + 1;
			else
				ans[i] = ans[i-1] + 1;
		}
		return ans;
	}
};

