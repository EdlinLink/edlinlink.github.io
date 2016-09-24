---
layout: post
title:  "Leetcode - 396. Rotate Function"
date:   2016-09-12 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [396. Rotate Function](https://leetcode.com/problems/rotate-function/)

Given an array of integers A and let n to be its length.

Assume Bk to be an array obtained by rotating the array A k positions clock-wise, we define a "rotation function" F on A as follow:

F(k) = 0 * Bk[0] + 1 * Bk[1] + ... + (n-1) * Bk[n-1].

Calculate the maximum value of F(0), F(1), ..., F(n-1).

**Note:**
n is guaranteed to be less than 10^5.

**Example:**

	A = [4, 3, 2, 6]
	
	F(0) = (0 * 4) + (1 * 3) + (2 * 2) + (3 * 6) = 0 + 3 + 4 + 18 = 25
	F(1) = (0 * 6) + (1 * 4) + (2 * 3) + (3 * 2) = 0 + 4 + 6 + 6 = 16
	F(2) = (0 * 2) + (1 * 6) + (2 * 4) + (3 * 3) = 0 + 6 + 8 + 9 = 23
	F(3) = (0 * 3) + (1 * 2) + (2 * 6) + (3 * 4) = 0 + 2 + 12 + 12 = 26
	
	So the maximum value of F(0), F(1), F(2), F(3) is F(3) = 26.


### 1. Analyse

OK, when I see the `n` can be 10^5, I guess I just cannot solve it directly, but why not try.

### 2. TLE Code

	class Solution {
		public:
	    int maxRotateFunction(vector<int>& A) {
	        int maxRotate = INT_MIN;
	        for(int i=0; i<A.size(); i++){
	            int sum = 0;
	            for(int j=0; j<A.size(); j++)
	                sum += A[j]*j;
	            maxRotate = max( maxRotate, sum );
	            A.push_back(A.front());
	            A.erase(A.begin());
	        }
	        return ( A.size() ? maxRotate : 0 );
	    }
	};

Opps, it really TLE. Now we analyse the problem again. F(1) - F(0) = Sum\_of\_Array - (n+1) * last\_item\_of\_F(0).

F(k) = F(k-1) + Sum\_of\_Array - (n+1) * Array[n-k]

### 3. AC Code

	class Solution {
	public:
	    int maxRotateFunction(vector<int>& A) {
			int F = 0, Sum = 0;
			for( int i=0; i<A.size(); i++ ){
				F += A[i]*i;
				Sum += A[i];
			}
			int maxRotate = max( F, INT_MIN );

			for( int i=1; i<A.size(); i++ ){
				F = F + Sum - A.size() * A[A.size()-i];
				maxRotate = max( maxRotate, F );
			}
			return ( A.size() ? maxRotate : 0 );
	    }
	};

