---
layout: post
title:  "Leetcode - 383. Ransom Note"
date:   2016-08-19 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [383. Ransom Note](https://leetcode.com/problems/ransom-note/)

Given an arbitrary ransom note string and another string containing letters from all the magazines, write a function that will return true if the ransom note can be constructed from the magazines; otherwise, it will return false.   

Each letter in the magazine string can only be used once in your ransom note.

Note:
You may assume that both strings contain only lowercase letters.

	canConstruct("a", "b") -> false
	canConstruct("aa", "ab") -> false
	canConstruct("aa", "aab") -> true

### 1. Analyse

It is a easy problem, we just need to count the quantity of each letter in magazine and compare with quantity of each letter in ransom not. We can use a map to store the quantity.

### 2. AC Code

(12 Lines)

	class Solution {
	public:
	    bool canConstruct(string ransomNote, string magazine) {
	        map<char,int> mp; 
	        for(int i=0; i<magazine.size(); i++)
	            mp[magazine[i]]++;
	        for(int i=0; i<ransomNote.size(); i++)
	            if(--mp[ransomNote[i]] < 0)
	                return false;
	        return true;
	    }
	};

Here we can learn one thing: if a map does not have a key, its value will initial to be 0.
