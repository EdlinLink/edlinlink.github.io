---
layout: post
title:  "Leetcode - 523. Continuous Subarray Sum"
date:   2017-05-12 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [523. Continuous Subarray Sum](https://leetcode.com/problems/continuous-subarray-sum/#/description)

Given a list of non-negative numbers and a target integer k, write a function to check if the array has a continuous subarray of size at least 2 that sums up to the multiple of k, that is, sums up to n * k where n is also an integer.

**Example 1:**

	Input: [23, 2, 4, 6, 7],  k=6
	Output: True
	Explanation: Because [2, 4] is a continuous subarray of size 2 and sums up to 6.

**Example 2:**

	Input: [23, 2, 6, 4, 7],  k=6
	Output: True
	Explanation: Because [23, 2, 6, 4, 7] is an continuous subarray of size 5 and sums up to 42.

**Note:**

1. The length of the array won't exceed 10,000.
2. You may assume the sum of all the numbers is in the range of a signed 32-bit integer.

---

### 1. Analyse

***Analyse a question well means solving it half. The most important thing is to analyse the question.***

If you have no idea on the question like me, why not write it down. Seeing is better than thinking in brain.

According to the description, we have the formula:

	a1 + a2 + a3 + ... + am = n * k

I don't like the unknow number `n`, so the formula can be:

	a1%k + a2%k + a3%k + ... + am%k = k

Now, all the number in array is smaller than k. The question is equal to:

*Given a list of non-negative numbers and a target integer k, all numbers are smaller than k. Write a function to check if the array has a continuous subarray of size at least 2 that sums up to k.*

---
### 2. Corner Case

1. If `k` is  0.

k cannot be divide or mod.

2. If `k` is negative number.

k can be transform to abs(k).

3. If array is `[0, 0]`.

Every k can be multiply by `0` to return true.

---
### 3. Wrong Code

	class Solution {
	public:
	    bool checkSubarraySum(vector<int>& nums, int k) {
			k = abs(k);
	 		for(int i=0; i<nums.size() && k!=0; i++)
				nums[i] = nums[i] % k;
			
			int sum = 0, i = -1, j = -1;
			while( i<(int)nums.size() && j<(int)nums.size() ){
				if( ((0==k && sum==k) || (0!=k && 0 == sum%k)) && j-i>=2 )
					return true;
	
				if( sum > k )
					sum -= nums[++i];
				else
					sum += nums[++j];	
			}
			return false;
	    }
	};

---
### 4. Where We Go Wrong

Consider this case 
	
	[7, 7, 6]	
	10

It really need to sum up all array number. But how we can find a `loop` of 10? 

If the sum up mod is appear twice, it means there is a loop.

---
### 5. AC Code

	class Solution {
	public:
	    bool checkSubarraySum(vector<int>& nums, int k) {
			map<int,int> mp;
			mp[0]++;
			k = abs(k);
			int sum = 0;
	
	 		for(int i=0; i<nums.size(); i++){
				sum += nums[i];
				int mod = ( 0==k ? sum : sum%k );
				mp[mod]++;
	
				if( 0 == k ){
					if( mp[mod]>=2 )	
						return true;
				}
				else{
					if( mp.find(mod) != mp.end() && i>0 )	
						return true;
				}
			}
			return false;
	    }
	};


---
### 6. Conclusion

This question has a lot of corner cases. It means if the future I should consider some case input such as:

	0
	-1
	
And when see the `multiply` in the description I should firstly come up with `mod`.
