---
layout: post
title:  "Leetcode - 394. Decode String"
date:   2016-09-06 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [394. Decode String](https://leetcode.com/problems/decode-string/)

Given an encoded string, return it's decoded string.

The encoding rule is: k\[encoded\_string\], where the encoded\_string inside the square brackets is being repeated exactly k times. Note that k is guaranteed to be a positive integer.

You may assume that the input string is always valid; No extra white spaces, square brackets are well-formed, etc.

Furthermore, you may assume that the original data does not contain any digits and that digits are only for those repeat numbers, k. For example, there won't be input like 3a or 2[4].

Examples:

	s = "3[a]2[bc]", return "aaabcbc".
	s = "3[a2[c]]", return "accaccacc".
	s = "2[abc]3[cd]ef", return "abcabccdcdcdef".

### 1. Analyse

Use a stack, when meets `]`, construct string between `[` and `]` and repeated it.

### 2. AC Code

(42 Lines)

    class Solution {
    public:
        bool isDigit( string s ){
            for( int i=0; i<s.size(); i++ )
                if( !(s[i]>='0' && s[i]<='9') )
                    return false;
            return true;
        }   

        string decodeString(string s) {
            vector<string> vec;
            for( int i=0; i<s.size(); i++ ){
                if( s[i]>='0' && s[i]<='9' ){
                    if( vec.empty() || !isDigit(vec.back()) )
                        vec.push_back( s.substr(i,1) );
                    else
                        vec.back() += s[i];
                }   
                else if( s[i] == '[' || (s[i]>='a' && s[i]<='z') )
                    vec.push_back( s.substr(i,1) );
                else if( s[i] == ']' ){
                    string str;
                    while( vec.back() != "[" ){
                        str = vec.back() + str;
                        vec.pop_back();
                    }   
                    vec.pop_back();
                    int repeat = stoi(vec.back());
                    vec.pop_back();

                    string repeat_str;
                    while( repeat-- ) 
                        repeat_str += str;

                    vec.push_back( repeat_str );  
                }   
            }           
            string ans;
            for( int i=0; i<vec.size(); i++ )
                ans += vec[i];
            return ans;
        }
    }; 

