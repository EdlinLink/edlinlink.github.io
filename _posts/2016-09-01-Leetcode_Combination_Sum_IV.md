---
layout: post
title:  "Leetcode - 377. Combination Sum IV"
date:   2016-09-01 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [377. Combination Sum IV](https://leetcode.com/problems/combination-sum-iv/)

Given an integer array with all positive numbers and no duplicates, find the number of possible combinations that add up to a positive integer target.

Example:

	nums = [1, 2, 3]
	target = 4

	The possible combination ways are:
	(1, 1, 1, 1)
	(1, 1, 2)
	(1, 2, 1)
	(1, 3)
	(2, 1, 1)
	(2, 2)
	(3, 1)

	Note that different sequences are counted as different combinations.

	Therefore the output is 7.

Follow up:
What if negative numbers are allowed in the given array?
How does it change the problem?
What limitation we need to add to the question to allow negative numbers?

### 1. Analyse

It is a typical backtracking/recursion problem. Sort the array and make a for loop.

### 2. TLE Code

	class Solution {
	public:
	    int combinationSum4(vector<int>& nums, int target) {
			if( target == 0 )
				return 1;
	
			int count = 0;
			for( int i=0; i<nums.size(); i++ ){
				if( target == nums[i] )
					count += 1;
				else if( target > nums[i] )
					count += combinationSum4( nums, target-nums[i] );
				else
					count += 0;
			}
			return count;
	    }
	};

If we use `recursion`, it will TLE. Let make it iteration. But I cannot write clean iteration code. I search the solution and found the best solution is DP.

### AC Code

class Solution {
public:
	int combinationSum4(vector<int>& nums, int target) {
		sort(nums.begin(), nums.end());

		vector<int> dp(target+1, 0);
		dp[0] = 1;
		for( int i=1; i<=target; i++ ){
			for( int j=0; j<nums.size(); j++ ){
				if( i >= nums[j] )
					dp[i] += dp[i-nums[j]];	
				else
					break;
			}
		}
		return dp.back();
	}
};

### Reference 

1. [\[LeetCode\] Combination Sum IV 组合之和之四](http://www.cnblogs.com/grandyang/p/5705750.html)
