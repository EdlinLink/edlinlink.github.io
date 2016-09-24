---
layout: post
title:  "Leetcode - 398. Random Pick Index"
date:   2016-09-13 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [398. Random Pick Index](https://leetcode.com/problems/random-pick-index/)

Given an array of integers with possible duplicates, randomly output the index of a given target number. You can assume that the given target number must exist in the array.

Note:
The array size can be very large. Solution that uses too much extra space will not pass the judge.

Example:

	int[] nums = new int[] {1,2,3,3,3};
	Solution solution = new Solution(nums);
	
	// pick(3) should return either index 2, 3, or 4 randomly. Each index should have equal probability of returning.
	solution.pick(3);
	
	// pick(1) should return 0. Since in the array only nums[0] is equal to 1.
	solution.pick(1);

### 1. Analyse

I don't know whether a hashmap can solve this problem. Let's try.

### 2. MLE Code

	class Solution {
	public:
		unordered_map<int, vector<int>> mp;

	    Solution(vector<int> nums) {
			mp.clear();
			for( int i=0; i<nums.size(); i++ )
				mp[nums[i]].push_back(i);
	    }
	    
	    int pick(int target) {
			int size = mp[target].size();
			return mp[target][rand()%size];
	    }
	};
	
	/**
	 * Your Solution object will be instantiated and called as such:
	 * Solution obj = new Solution(nums);
	 * int param_1 = obj.pick(target);
	 */

WTF. I cannot believe my solution is MLE. Using less memory means using more time.

### 3. AC Code

	class Solution {
	public:
		vector<int> vec;
	    Solution(vector<int> nums) {
			vec = nums;
	    }
	    
	    int pick(int target) {
			int count = 0;
			int ans;
			for( int i=0; i<vec.size(); i++ )
				if( (vec[i] == target) && (rand()%++count == 0) )
					ans = i;
			return ans;
	    }
	};

### Reference

1. [398. Random Pick Index. reboot329.](http://www.cnblogs.com/reboot329/p/5868202.html)
