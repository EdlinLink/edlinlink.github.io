---
layout: post
title:  "Leetcode - 376. Wiggle Subsequence"
date:   2016-07-23 12:00:00
tags:	technology
myTag:	algorithm
headimg: swing.jpg

---

### [376. Wiggle Subsequence](https://leetcode.com/problems/wiggle-subsequence/)

A sequence of numbers is called a wiggle sequence if the differences between successive numbers strictly alternate between positive and negative. The first difference (if one exists) may be either positive or negative. A sequence with fewer than two elements is trivially a wiggle sequence.

For example, [1,7,4,9,2,5] is a wiggle sequence because the differences (6,-3,5,-7,3) are alternately positive and negative. In contrast, [1,4,7,2,5] and [1,7,4,5,5] are not wiggle sequences, the first because its first two differences are positive and the second because its last difference is zero.

Given a sequence of integers, return the length of the longest subsequence that is a wiggle sequence. A subsequence is obtained by deleting some number of elements (eventually, also zero) from the original sequence, leaving the remaining elements in their original order.

**Examples:**

	Input: [1,7,4,9,2,5]
	Output: 6
	The entire sequence is a wiggle sequence.
	
	Input: [1,17,5,10,13,15,10,5,16,8]
	Output: 7
	There are several subsequences that achieve this length. One is [1,17,10,13,10,16,8].
	
	Input: [1,2,3,4,5,6,7,8,9]
	Output: 2

**Follow up:**

Can you do it in O(n) time?

### 1. Analyse

To be honest, I have no idea how to solve this problem at first. But recently I have a read a article about a Google interviewee who don't know how to solve the interview problem. But he analysed the problem from the easiest case and convince the interviewer to hire him.

Let's see the problem. The `wiggle` is defined as the difference are alternately positive and negative. So it is defined as the numbers are larger or smaller than both of its neighbour. (We have make it easier, let's go on.)

We take the example2 as our example [1,17,5,10,13,15,10,5,16,8]. 

	1 < 17 > 5 < 10 < 13

The first four numbers go well. When the fifth number `13` is visited, we have to remove `13` because it is not smaller than `10`. But wait, why don't we remove `10` and keep `13` in the sequence? Yes, we should keep `13` rather than `10` because [1,17,5,13] has more possbility to have larger lenght than [1,17,5,10]. The process of making sequence will like these:

	1
	1 < 17
	1 < 17 > 5
	1 < 17 > 5 < 10
	1 < 17 > 5 < max(10,13)
	1 < 17 > 5 < max(13,15)
	1 < 17 > 5 < 15 > 10
	1 < 17 > 5 < 15 > min(10,5)
	1 < 17 > 5 < 15 > 5 < 16
	1 < 17 > 5 < 15 > 5 < 16 > 8

There is one more thing we have to concern. The process of example 2 is under the hypothesis subsequence the first number is less than the second one. We have to consider another case the first number is larger than the second one.

### 2. AC Code

(33 Lines)

	class Solution {
	public:
	    int wiggleMaxLength(vector<int>& nums) {
	        int max_length = ( nums.empty() ? 0 : 1 );
	
	        for( int i=1; i<nums.size(); i++ ){
	            if( max_length % 2 == 0 ){
	                if( nums[i] > nums[i-1] )
	                    max_length ++;
	            }
	            else{
	                if( nums[i] < nums[i-1] )
	                    max_length ++;
	            }
	        }
	
	        int max_length_up = max_length;
	        max_length = ( nums.empty() ? 0 : 1 );
	
	        for( int i=1; i<nums.size(); i++ ){
	            if( max_length % 2 == 0 ){
	                if( nums[i] < nums[i-1] )
	                    max_length ++;
	            }
	            else{
	                if( nums[i] > nums[i-1] )
	                    max_length ++;
	            }
	        }
	
	        return max( max_length, max_length_up );
	    }
	};


