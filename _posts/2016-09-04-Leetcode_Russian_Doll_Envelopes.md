---
layout: post
title:  "Leetcode - 354. Russian Doll Envelopes"
date:   2016-09-04 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [354. Russian Doll Envelopes](https://leetcode.com/problems/russian-doll-envelopes/)

You have a number of envelopes with widths and heights given as a pair of integers (w, h). One envelope can fit into another if and only if both the width and height of one envelope is greater than the width and height of the other envelope.

What is the maximum number of envelopes can you Russian doll? (put one inside other)

Example:

Given envelopes = [[5,4],[6,4],[6,7],[2,3]], the maximum number of envelopes you can Russian doll is 3 ([2,3] => [5,4] => [6,7]).

### 1. Analyse

1. Very likely the Longest Increasing Subsequence. We can review the LIS problem first.

### 2. AC Code

(23 Lines)

	class Solution {
	public:
	    static bool cmp( const pair<int,int> & a, pair<int,int>& b ){
	            return a.first==b.first ? a.second<b.second : a.first<b.first;
	    }
	
	    int maxEnvelopes(vector<pair<int, int> >& envelopes) {
	        sort( envelopes.begin(), envelopes.end(), cmp);                         
	
	        int ans = 0;
	        vector<int>vec;
	        for( int i=0; i<envelopes.size(); i++ ){
	            int contain = 1;
	            for( int j=0; j<vec.size(); j++ ){
	                if( envelopes[i].first > envelopes[j].first && envelopes[i].second > envelopes[j].second )
	                    contain = max( vec[j]+1, contain );
	            }   
	            vec.push_back( contain );
	            ans = max( ans, contain );
	        }   
	        return ans;
	    }   
	};


