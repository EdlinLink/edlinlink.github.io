---
layout: post
title:  "Leetcode - 373.Find K Pairs with Smallest Sums"
date:   2016-07-09 12:00:00
tags:	technology
myTag:	algorithm
headimg: silicon_valley_map.jpg

---

### [373. Find K Pairs with Smallest Sums](https://leetcode.com/problems/find-k-pairs-with-smallest-sums/)

You are given two integer arrays nums1 and nums2 sorted in ascending order and an integer k.

Define a pair (u,v) which consists of one element from the first array and one element from the second array.

Find the k pairs (u1,v1),(u2,v2) ...(uk,vk) with the smallest sums.

Example 1:
	Given nums1 = [1,7,11], nums2 = [2,4,6],  k = 3
	
	Return: [1,2],[1,4],[1,6]
	
	The first 3 pairs are returned from the sequence:
	[1,2],[1,4],[1,6],[7,2],[7,4],[11,2],[7,6],[11,4],[11,6]

Example 2:
	Given nums1 = [1,1,2], nums2 = [1,2,3],  k = 2
	
	Return: [1,1],[1,1]
	
	The first 2 pairs are returned from the sequence:
	[1,1],[1,1],[1,2],[2,1],[1,2],[2,2],[1,3],[1,3],[2,3]

Example 3:
	Given nums1 = [1,2], nums2 = [3],  k = 3 
	
	Return: [1,3],[2,3]
	
	All possible pairs are returned from the sequence:
	[1,3],[2,3]

### 1. Analyse

After getting this problem, you must have thought about the `Brute Force`. OK, let's try it. Now the problem comes to what kind of container should we use? We should store the sum of pairs and sort them. The sum and the pair can be duplicated. Let's use `multimap`.

### 2. Brute Force

(16 Lines)

	class Solution {
	public:
	    vector<pair<int, int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
	        vector<pair<int,int>> vec;
	        multimap<int, pair<int,int>> mp;
	        for( int i=0; i<nums1.size(); i++ ){
	            for( int j=0; j<nums2.size(); j++ ){
	                mp.insert( { nums1[i]+nums2[j], pair<int,int>(nums1[i], nums2[j]) } );
	            }
	        }
	
	        for( multimap<int,pair<int,int>>::iterator it=mp.begin(); it!=mp.end() && vec.size()<k; it++ )   
	            vec.push_back( it->second );
	        return vec;
	    }
	};	

I can't believe it just AC. Even though it AC, we can still improve the algorithm and discuss about the multimap. The problem only ask us to return the smallest K pair, so we don't need to calculate and store all pairs.

### 3. Improved Brute Force

(14 Lines)

    class Solution {
    public:
        vector<pair<int, int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
            vector<pair<int,int>> vec;
            multimap<int, pair<int,int>> mp;
            for( int i=0; i<nums1.size() && i<k; i++ )
                for( int j=0; j<nums2.size() && j<k; j++ )
                    mp.insert( { nums1[i]+nums2[j], pair<int,int>(nums1[i], nums2[j]) } );
    
            for( multimap<int,pair<int,int>>::iterator it=mp.begin(); it!=mp.end() && vec.size()<k; it++ )   
                vec.push_back( it->second );
            return vec;
        }
    }; 

### 4. Multimap

I have never use `multimap` before this problem and I even don't know the key in map is sorted in order (what a good attribute). Let see the function prototype of `map`:

	template < class Key, class T, class Compare = less<Key>, class Allocator = allocator<pair<const Key,T> > > class map;  

The third argument is a compare template default value is `less` function.

	template <class T> struct less : binary_function <T,T,bool> {  
		bool operator() (const T& x, const T& y) const  {
			return x<y;
		}  
	};  	

Apart from `less` function, these is a `greater` function:

	template <class T> struct greater : binary_function <T,T,bool> {  
 		bool operator() (const T& x, const T& y) const {
			return x>y;
		}  
	};  

The key in the `map` is sorted according to the third argument. Sort Defaultly by ascending. If we want the key to be sorted in descending, we need to set the argument to be `greater<T>`.

Wow, I have learned something new today. The cover picture is reference as the map.

### Reference

1. [[LeetCode] Find K Pairs with Smallest Sums 找和最小的K对数字](http://www.cnblogs.com/grandyang/p/5653127.html)
2. [C++ STL中Map的按Key排序和按Value排序](http://blog.csdn.net/acidgl8757/article/details/17416439)
