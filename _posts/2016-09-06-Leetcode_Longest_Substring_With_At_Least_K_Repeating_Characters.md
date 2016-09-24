---
layout: post
title:  "Leetcode - 395. Longest Substring with At Least K Repeating Characters"
date:   2016-09-06 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [395. Longest Substring with At Least K Repeating Characters](https://leetcode.com/problems/longest-substring-with-at-least-k-repeating-characters/)

Find the length of the longest substring T of a given string (consists of lowercase letters only) such that every character in T appears no less than k times.

Example 1:

	Input:
	s = "aaabb", k = 3
	
	Output:
	3

	The longest substring is "aaa", as 'a' is repeated 3 times.

Example 2:

	Input:
	s = "ababbc", k = 2
	
	Output:
	5
	
	The longest substring is "ababb", as 'a' is repeated 2 times and 'b' is repeated 3 times.

### 1. Analyse

Statistic the frequency of each letter and recursive return the longestSubString.

### 2. MLE Code

	class Solution {
	public:
		int longestSubstring(string s, int k )
		{
			int ans = 0;
			vector<int> count(26,0);			        
			for( int i=0; i<s.size(); i++ )
				count[s[i]-'a']++;
	
			for( int i=0; i<s.size(); i++ )
				if( count[s[i]-'a']<k )
					return max( longestSubstring(s.substr(0,i),k), longestSubstring(s.substr(i+1),k) );
			return s.size();
		}
	};

Using recursion will lead to Memory Limit Exceeded. We do not need to calculate `longestSubstring` if the length of the string is less than current answer;

### 3. TLE Code

	class Solution {
	public:
		int longestSubstring(string s, int k )
		{
			int ans = 0;
			vector<int> count(26,0);			        
			for( int i=0; i<s.size(); i++ )
				count[s[i]-'a']++;
	
			for( int i=0; i<s.size(); i++ ){
				if( count[s[i]-'a']<k ){
					int left = 0, right = 0;
					left = longestSubstring(s.substr(0,i),k);
					if( s.substr(i+1).size() > left)
						right = longestSubstring(s.substr(i+1),k);
					return max( left, right );
				}
			}
			return s.size();
		}
	};

Opps, recursion solution still Time Limit Exceeded. We now just split the substring into two parts, but actually after one statistic we can split the string into several parts.

### 4. Code

	class Solution {
	public:
		int longestSubstring(string s, int k )
		{
			int ans = 0;
			vector<int> count(26,0);			        
			for( int i=0; i<s.size(); i++ )
				count[s[i]-'a']++;
	
			int len = -1;
			for( int i=0; i<s.size(); i++ ){
				int index = 0;
				if( count[s[i]-'a']<k ){
					len = max( len, longestSubstring( s.substr(index, i-index), k ) );
					index = i+1;
				}
			}
			return ( -1 == len ? s.size() : len );
		}
	};

