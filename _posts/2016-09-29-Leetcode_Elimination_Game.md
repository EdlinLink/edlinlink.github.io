---
layout: post
title:  "Leetcode - 390. Elimination Game"
date:   2016-09-29 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [390. Elimination Game](https://leetcode.com/problems/elimination-game/)

There is a list of sorted integers from 1 to n. Starting from left to right, remove the first number and every other number afterward until you reach the end of the list.

Repeat the previous step again, but this time from right to left, remove the right most number and every other number from the remaining numbers.

We keep repeating the steps again, alternating left to right and right to left, until a single number remains.

Find the last number that remains starting with a list of length n.

Example:

	Input:
	n = 9,
	`1` 2 `3` 4 `5` 6 `7` 8 `9`
	2 `4` 6 `8`
	`2` 6
	6
	
	Output:
	6

### 1. Analyse

Let's start from the easy way - Simulation. And then we may find some hints.

### 2. TLE Code

	class Solution {
	public:
		int lastRemaining(int n) {
			vector<int> vec;
			for( int i=1; i<=n; i++ )
				vec.push_back(i);

			while( 1 != vec.size() )
			{
				for( int i=0; i<vec.size(); i+=1 )
					vec.erase( vec.begin()+i );
				reverse(vec.begin(), vec.end());
			}

			return vec[0];
		}
	};

Of course simulation will lead to TLE. 

Let's start `n` from 1 to 10;

	n=1	
	1

	n=2
	1,2
	2

	n=3
	1,2,3
	2

	n=4
	1,2,3,4
	2,4
	2

	n=5
	1,2,3,4,5
	2,4

	n=6
	1,2,3,4,5,6
	2,4,6
	4

	n=7
	1,2,3,4,5,6,7
	2,4,6
	4

	n=8
	1,2,3,4,5,6,7,8
	2,4,6,8
	2,6
	6

	n=9
	1,2,3,4,5,6,7,8,9
	2,4,6,8	
	2,6
	6		

	n=10
	1,2,3,4,5,6,7,8,9,10
	2,4,6,8,10 --> (1,2,3,4,5) x2
	4,8
	8	

If now `n` is odd. Remove from left to right:

	n=N
	1,2,3,...,N-1,N
	2,4,...,N-3,N-1 --> 1,2,...,(N-3)/2, (N-1)/2		x2
	
If now `n` is odd. Remove from right to left:

	n=N
	1,2,3,...,N-1,N
	2,4,...,N-3,N-1	--> 1,2,...,(N-3)/2, (N-1)/2		x2

If now `n` is even. Remove from left to right:

	n=N
	1,2,3,...,N-1,N
	2,4,...,N --> 1,2,...,N/2		x2

If now `n` is even. Remove from right to left:

	n=N
	1,2,3,...N-1,N
	1,3,...,N-1 --> 1,2,..,N/2		x2-1

### 3, AC Code

	class Solution {
	public:
		int lastRemaining(int n) {
			return remove(n, true);
		}
		int remove( int n, bool left2right ){
			if( n==1 )						return 1;
			if( n%2 != 0 || left2right )	return remove( n/2, !left2right ) *2;
			else							return remove( n/2, !left2right ) *2 -1;
		}
	};

