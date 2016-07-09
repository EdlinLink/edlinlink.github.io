---
layout: post
title:  "Leetcode - 368. Largest Divisible Subset"
date:   2016-07-09 12:00:00
tags:	technology
myTag:	algorithm
headimg: Russia_Doll.jpg

---
### [368. Largest Divisible Subset](https://leetcode.com/problems/largest-divisible-subset/)

Given a set of distinct positive integers, find the largest subset such that every pair (Si, Sj) of elements in this subset satisfies: Si % Sj = 0 or Sj % Si = 0.

If there are multiple solutions, return any subset is fine.

Example 1:

	nums: [1,2,3]
	
	Result: [1,2] (of course, [1,3] will also be ok)

Example 2:

	nums: [1,2,4,8]

	Result: [1,2,4,8]

### 1. Analyse

It is better to sorted the array first. If a number N can be divided by another number M ( M % N == 0 ), then all the numbers that can be divided by N also can be divided by M ( if N % x == 0, then M % x == 0 ).

Let's define the total numbers that can be devided by N is T(N), then T(M) >= T(N)+1. It's easy to find the number X that have the largest T(X), but we still need to record those numbers that can be divided by X. We need a index.

### 2. AC Code

(28 Lines )

	class Solution {
	public:
	    vector<int> largestDivisibleSubset(vector<int>& nums) {
	        sort(nums.begin(), nums.end());
	        
	        vector<int> result;
	        vector<int> count(nums.size(), 1);
	        vector<int> index(nums.size(), -1);
	        int max_index = nums.size()-1;
	
	        for( int i=0; i<nums.size(); i++ ){
	            for( int j=0; j<i; j++ ){
	                if( nums[i] % nums[j] == 0 && count[i] < count[j]+1 ){
	                    count[i] = count[j]+1;
	                    index[i] = j;
	                }
	            }
	            if( count[i] >= count[max_index] )
	                max_index = i;
	        }
	
	        while( max_index >=0 ){
	            result.insert( result.begin(),  nums[max_index] );
	            max_index = index[max_index];
	        }
	        return result;
	    }
	};

### 3. What Can Be Improved

Let's see the time complexity of the solution is O(n^2). The second loop j can be started from descending order. What's more, if we recored the current max count of each position, then we can add one more strictly condition in the inner loop `count[i] < max_count[j]`. Because if we have known there is no more chance we can get biger count, we can break the inner loop. And we can also easily get the max\_index.


The cover picture is Russia Dolls, as a symbol of subset. Hope you like it.

