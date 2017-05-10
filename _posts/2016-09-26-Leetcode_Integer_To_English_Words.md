---
layout: post
title:  "Leetcode - 273. Integer to English Words"
date:   2016-09-26 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [273. Integer to English Words](https://leetcode.com/problems/integer-to-english-words/)

Convert a non-negative integer to its english words representation. Given input is guaranteed to be less than 2^31 - 1.

For example,

	123 -> "One Hundred Twenty Three"
	12345 -> "Twelve Thousand Three Hundred Forty Five"
	1234567 -> "One Million Two Hundred Thirty Four Thousand Five Hundred Sixty Seven"

### 1. Analyse

Easy problem may have tough process.

Split the number digits into groups, every three digits is one group. (1234567 --> 1,234,567)

### 2. AC Code

(56 Lines)

    class Solution {
    public:
        map<int,string> digit_one;
        map<int,string> digit_ten;

        void init(){
            digit_one[0] = "";      digit_one[1] = "One";   digit_one[2] = "Two";   digit_one[3] = "Three"; digit_one[4] = "Four";  
            digit_one[5] = "Five";  digit_one[6] = "Six";   digit_one[7] = "Seven"; digit_one[8] = "Eight"; digit_one[9] = "Nine";  

            digit_ten[10] = "Ten";      digit_ten[11] = "Eleven";   digit_ten[12] = "Twelve";   digit_ten[13] = "Thirteen"; 
            digit_ten[14] = "Fourteen"; digit_ten[15] = "Fifteen";  digit_ten[16] = "Sixteen";  digit_ten[17] = "Seventeen";    
            digit_ten[18] = "Eighteen"; digit_ten[19] = "Nineteen"; digit_ten[2] = "Twenty";    digit_ten[3] = "Thirty";
            digit_ten[4] = "Forty";     digit_ten[5] = "Fifty";     digit_ten[6] = "Sixty";     digit_ten[7] = "Seventy";   
            digit_ten[8] = "Eighty";    digit_ten[9] = "Ninety";    
        }

        string IntToStr( int num ){
            string str;
            int hundred = num/100, ten = (num%100)/10, one = num%10;

            if( 0 != hundred )
                str += (digit_one[ hundred ] + " Hundred" );
            
            if( 0 != ten ){
                if( !str.empty() )  str += " ";
				str =  (1==ten) ? str+digit_ten[10*ten+one] : str+digit_ten[ten];
            }

            if( 0 != one && 1 != ten )
				str = str.empty() ? digit_one[one] : (str + " " + digit_one[one]);

            return str;
        }

        string numberToWords(int num) {
            init();
            vector<int> group;
            while( num ){
                group.push_back( num % 1000 );
                num /= 1000;
            }
            vector<string> unit;
            unit.push_back("");         
            unit.push_back(" Thousand");    
            unit.push_back(" Million"); 
            unit.push_back(" Billion");

            string ret;
            for( int i=0; i<group.size(); i++ ){
                string tmp = IntToStr(group[i]);
                if( !tmp.empty() ){
                    ret = ret.empty() ? tmp+unit[i] : (tmp+unit[i] + " " + ret);
                }
            }
            return ret.empty() ? "Zero" : ret;
        }
    };

