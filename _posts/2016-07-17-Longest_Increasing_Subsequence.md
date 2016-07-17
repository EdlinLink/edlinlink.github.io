---
layout: post
title:  "Leetcode - 300. Longest Increasing Subsequence"
date:   2016-07-17 12:00:00
tags:	technology
myTag:	algorithm
headimg: Baiyoke.jpg

---

### [300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

Given an unsorted array of integers, find the length of longest increasing subsequence.

For example,

Given [10, 9, 2, 5, 3, 7, 101, 18],

The longest increasing subsequence is [2, 3, 7, 101], therefore the length is 4. Note that there may be more than one LIS combination, it is only necessary for you to return the length.

Your algorithm should run in O(n^2) complexity.

Follow up: Could you improve it to O(n log n) time complexity?

### 1. Analyse

This is a typical problem called "Longest Increasing Subsequence". For each num, if it is the end of the sequence, then the maximun subsequence is its previous smaller num sequence plus 1. An O(n^2) solution is to solve the sub-problem of its prvious sequence. Then travel the previous result and plus one if the target number is larger than the previous sub-sequence number.

### 2. O(n^2) AC Code

(18 Lines)

	class Solution {
	public:
	    int lengthOfLIS(vector<int>& nums) {
	        int LIS = 0;
	        vector<int> ans( nums.size(), 1 );
	
	        for( int i=0; i<nums.size(); i++ ){
	            for( int j=0; j<i; j++ ){
	                if(nums[i] > nums[j] ){
	                    ans[i] = max( ans[j]+1, ans[i] );
	                }
	            } 
				LIS = max( ans[i], LIS );
	        }
	
	        return LIS;
	    }
	};

If we can find the largest nums[j] that just smaller a little bit than the nums[i], then we can accelarate the algorithm; If we have a sequence [1, 9, 10, 4, 5], we should record [1, 4, 5] rather than [1, 9, 10]. I will show the O(nlogn) solution first and explain later. 

### 3. O(nlogn) AC Code

(14 Lines)

	class Solution {
	public:
	    int lengthOfLIS(vector<int>& nums) {
	        vector<int> pos;
	        for( int i=0; i<nums.size(); i++ ){
	            vector<int> :: iterator p = lower_bound( pos.begin(), pos.end(), nums[i] );
	            if( p == pos.end() || *p > pos.back() )
	                pos.push_back( nums[i] );
	            else
	                *p = nums[i];
	        }
	        return pos.size();
	    }
	};

I use the vector to store the number. If the coming number is larger than the last one in vector, I add it to the vector; If the coming number is smaller than the last one in vector, I find the largest number that smaller than the coming one and replace it. The reason is [a ,nums[x1], b] has less posibility to have longer sequence than [a, nums[x2], b] when `x1 < x2` and `nums[x1] > nums[x2]`. **Pay attention that what store in the vector is not the subsequence, but the length is same as the subsequence.**

### 4. Follow up

*If we also want to return not only the length but also the subsequence, how can we modify our code?*

For O(n^2) solution, we need one more vector to trace back the index of number. That is to say, we need to know which number makes`nums[i] = nums[j]+1`. 

For O(nlogn) solution, we need a vector to record the length of each number to be the end of sequence. For example [1, 9, 10, 2, 3], we record [1, 2, 3, 2, 3]. We pick the numbers from end to the front according to the record vector.

---

The cover picture is the tallest building in Bangkok, Baiyoke Sky Building. 
