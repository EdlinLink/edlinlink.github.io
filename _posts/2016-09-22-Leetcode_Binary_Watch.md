---
layout: post
title:  "Leetcode - 401. Binary Watch"
date:   2016-09-22 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [401. Binary Watch](https://leetcode.com/problems/binary-watch/)

A binary watch has 4 LEDs on the top which represent the hours (0-11), and the 6 LEDs on the bottom represent the minutes (0-59).

Each LED represents a zero or one, with the least significant bit on the right.

For example, the above binary watch reads "3:25".

Given a non-negative integer n which represents the number of LEDs that are currently on, return all possible times the watch could represent.

Example:

	Input: n = 1
	Return: ["1:00", "2:00", "4:00", "8:00", "0:01", "0:02", "0:04", "0:08", "0:16", "0:32"]

Note:

+ The order of output does not matter.
+ The hour must not contain a leading zero, for example "01:00" is not valid, it should be "1:00".
+ The minute must be consist of two digits and may contain a leading zero, for example "10:2" is not valid, it should be "10:02".

### 1. Analyse

Do a loop of all time possibility and count the `1` bits of the binary expression.

### 2. AC Code

(32 Lines)

    class Solution {
    public:
        vector<string> readBinaryWatch(int num) {
            vector<string> ret;
            map<int, vector<int> > m;
            for( int i=0; i<60; i++ ){
                int count = 0, tmp = i;
                while( tmp ){  
                    tmp = tmp & (tmp-1);
                    count++;
                }   
                m[count].push_back( i );
            }   

            for( int i=0; i<=num; i++ ){
                vector<int> hour = m[i];
                for( int j=0; j<hour.size(); j++ ){
                    int h = hour[j];
                    if( h < 12 ){
                        vector<int> min = m[num-i];
                        for( int k=0; k<min.size(); k++ ){
                            if( min[k] <10 )
                                ret.push_back( to_string(h)+":0"+to_string(min[k]) );
                            else
                                ret.push_back( to_string(h)+":"+to_string(min[k]) );
                        }   
                    }   
                }   
            }                           
            return ret;
        }   
    };
