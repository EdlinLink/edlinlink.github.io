---
layout: post
title:  "Leetcode - 416. Partition Equal Subset Sum"
date:   2016-10-09 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [416. Partition Equal Subset Sum](https://leetcode.com/problems/partition-equal-subset-sum/)

Given a non-empty array containing only positive integers, find if the array can be partitioned into two subsets such that the sum of elements in both subsets is equal.

Note:
Both the array size and each of the array element will not exceed 100.

Example 1:

	Input: [1, 5, 11, 5]
	
	Output: true
	
	Explanation: The array can be partitioned as [1, 5, 5] and [11].

Example 2:

	Input: [1, 2, 3, 5]
	
	Output: false
	
	Explanation: The array cannot be partitioned into equal sum subsets.

### 1. Analyse

If partition array into equal subset, then the sum of each subset is half of the total sum. Now the problem comes to whether we can pick up some elements so that the sum will equal to half total sum.

### 2. AC Code
	
	class Solution {
	public:
	    bool canPartition(vector<int>& nums) {
			int total = 0;
			for( int i=0; i<nums.size(); i++ )
	        	total += nums[i];

			if( total % 2 )	return false;
			sort( nums.begin(), nums.end() );
			return canSumHalf( nums, total/2, 0 );
	    }

		bool canSumHalf( vector<int> & nums, int target, int start ){
			if( 0 == target )	return true;
			for( int i=start; i<nums.size(); i++ ){
				if( target < nums[i] )	return false;
				if( canSumHalf( nums, target-nums[i], start+1) )	return true;
			}
			return false;
		}
	};

