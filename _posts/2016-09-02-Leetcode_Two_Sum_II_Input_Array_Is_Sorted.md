---
layout: post
title:  "Leetcode - 167. Two Sum II - Input array is sorted"
date:   2016-09-02 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [167. Two Sum II - Input array is sorted](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

Given an array of integers that is already sorted in ascending order, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are not zero-based.

You may assume that each input would have exactly one solution.

Input: numbers={2, 7, 11, 15}, target=9

Output: index1=1, index2=2

### 1. Analyse

Make two pointers point to the head and the tail of the array. Sum the pointed elements. If the sum greater than the target, the tail pointer move backward; If the sum smaller than the target, the head pointer move forward. 

### 2. AC Code

(16 Lines)
	
	class Solution {
	public:
		vector<int> twoSum(vector<int>& numbers, int target) {
			vector<int> ans;
			int i=0, j=numbers.size()-1;
			while( numbers[i]+numbers[j] != target ){
				if( numbers[i]+numbers[j] > target )
					j--;
				else
					i++;
			}
			ans.push_back(i+1);
			ans.push_back(j+1);
			return ans;
		}
	};

