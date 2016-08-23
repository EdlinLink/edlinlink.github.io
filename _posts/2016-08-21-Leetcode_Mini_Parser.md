---
layout: post
title:  "Leetcode - 385. Mini Parser"
date:   2016-08-22 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [385. Mini Parser](https://leetcode.com/problems/mini-parser/)

Given a nested list of integers represented as a string, implement a parser to deserialize it.

Each element is either an integer, or a list -- whose elements may also be integers or other lists.

Note: You may assume that the string is well-formed:

+ String is non-empty.
+ String does not contain white spaces.
+ String contains only digits `0-9`, `[`, `-`,`,`, `]`.

Example 1:

	Given s = "324",
	
	You should return a NestedInteger object which contains a single integer 324.

Example 2:

	Given s = "[123,[456,[789]]]",
	
	Return a NestedInteger object containing a nested list with 2 elements:
	
	1. An integer containing value 123.
	2. A nested list containing two elements:
	    i.  An integer containing value 456.
	    ii. A nested list with one element:
	         a. An integer containing value 789.

### 1. Analyse

Everytime when I see the bracket, the data structure stack(queue) comes to my mind. The string "[123,[456,[789]]]" becomes a queue structure:

	]
	]
	]
	789
	[
	456
	[
	123
	[

After thinking for a while, I review the target this problem want to reach. I need to construct a NestedInteger. How can I construct such a data structure? Using recursion or iteration? Why don't I ues the same data structure stack(queue) as the input data?

If I see a `[`, I construct a NestedInteger and put into a stack. If I see a number, I put the number into the top NestedInteger. If I see a `]`, I pop the NestedInteger.

### 2. AC Code

(47 Lines + 29 Commment)

	/**
	 * // This is the interface that allows for creating nested lists.
	 * // You should not implement it, or speculate about its implementation
	 * class NestedInteger {
	 *   public:
	 *     // Constructor initializes an empty nested list.
	 *     NestedInteger();
	 *
	 *     // Constructor initializes a single integer.
	 *     NestedInteger(int value);
	 *
	 *     // Return true if this NestedInteger holds a single integer, rather than a nested list.
	 *     bool isInteger() const;
	 *
	 *     // Return the single integer that this NestedInteger holds, if it holds a single integer
	 *     // The result is undefined if this NestedInteger holds a nested list
	 *     int getInteger() const;
	 *
	 *     // Set this NestedInteger to hold a single integer.
	 *     void setInteger(int value);
	 *
	 *     // Set this NestedInteger to hold a nested list and adds a nested integer to it.
	 *     void add(const NestedInteger &ni);
	 *
	 *     // Return the nested list that this NestedInteger holds, if it holds a nested list
	 *     // The result is undefined if this NestedInteger holds a single integer
	 *     const vector<NestedInteger> &getList() const;
	 * };
	 */
	class Solution {
	public:
	    NestedInteger deserialize(string s) {
	        vector<string> vec;
	        vec.push_back("");
	        for( int i=0; i<s.size(); i++ ){
	            switch (s[i]){
	                case '[':
	                    vec[vec.size()-1] += s[i];
	                    vec.push_back("");
	                    break;
	                case ']':
	                    if(vec[vec.size()-1] == "")
	                        vec[vec.size()-1] = s[i];
	                    else
	                        vec.push_back( "]" );
	                    break;
	                case ',':
	                    vec.push_back("");
	                    break;
	                default:
	                    vec[vec.size()-1] += s[i];
	                    break;
	            }
	        }
	        vector<NestedInteger> ans;
	        for( int i=0; i<vec.size(); i++ ){
	            if( vec[i] == "[" ){
	                ans.push_back( NestedInteger() );
	            }   
	            else if( vec[i] == "]" ){
	                NestedInteger tmp = ans.back();
	                ans.pop_back();
	                if( ans.empty() )
	                    return tmp;
	                else
	                    ans[ans.size()-1].add(tmp);
	            }
	            else{
	                if( ans.empty() )
	                    return NestedInteger( stoi(vec[i]) );
	                ans.back().add( NestedInteger(stoi(vec[i])) );
	            }
	        }
	        return NestedInteger();
	    }
	};

We should handle such test case `345` && `[345]`. To be honest, when I check other's code, I found there is no need to store the string into a vector but scan it on the fly.

### 2. AC Solution

(37 Lines)

	class Solution {
	public:
	    string getNum( const string &s, int &index ){
	        string ans;
	        while( index<s.size() && ( s[index]=='-' || (s[index]>='0' && s[index]<='9') ) )
	                ans += s[index++];
	        index-=1;
	        return ans;
	    }
	
	    NestedInteger deserialize(string s) {
	        vector<NestedInteger> ans;
	        for( int i=0; i<s.size(); i++ ){
	            if( s[i] == '[' ){
	                ans.push_back( NestedInteger() );
	            }   
	            else if( s[i] == ']' ){
	                NestedInteger item = ans.back();
	                ans.pop_back();
	                if( ans.empty() )
	                    return item;
	                else
	                    ans[ans.size()-1].add(item);
	            }
	            else if( s[i] == ',' ){
	                ;
	            }
	            else{
	                string num = getNum( s, i );
	                if( ans.empty() )
	                    return NestedInteger( stoi(num) );
	                ans.back().add( NestedInteger(stoi(num)) );
	            }
	        }
	        return NestedInteger();
	    }
	};
