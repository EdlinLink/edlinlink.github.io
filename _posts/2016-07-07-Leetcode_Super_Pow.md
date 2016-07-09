---
layout: post
title:  "Leetcode - 372. Super Pow"
date:   2016-07-07 12:00:00
tags:	technology
myTag:	algorithm
headimg: Peter_Montgomery.jpg

---

### [372. Super Pow](https://leetcode.com/problems/super-pow/)

Your task is to calculate a^b mod 1337 where a is a positive integer and b is an extremely large positive integer given in the form of an array.

Example1:

    a = 2
    b = [3]

    Result: 8

Example2:

    a = 2
    b = [1,0]
    
    Result: 1024

### 1. Analyse

The more simple the problem is, the hard the solution is. This problem should be classified as `Number Theory` problem.

Let's see a simple formula first:

	Let		a1 = a mod c,
			b1 = b mod c,
	
	Then	(a * b) mod c	=	[(nc+a1) * (mc+b1)] mod c
							=	(a1 * b1) mod c
							=	[(a mod c) * (b mod c)] mod c

We get the formula:

	(a * b) mod c = (a mod c) * (b mod c) mod c

**If b is even, then:**
	
	a^b mod c = [a^(b/2) mod c] * [a^(b/2) mod c] mod c;
			  = [a^(b/2) mod c]^2 mod c

			  = (a^2)^(b/2) mod c

**If b is odd, then:**

	a^b mod c = {a^[(b-1)/2] mod c} * {a^[(b-1)/2] mod c} * a mod c;
			  = {a^[(b-1)/2] mod c}^2 * a mod c
			  

Now it is intuitive to recursively divide b to be 1.


### 2. TLE Code

	class Solution {
	public:
	    vector<int> & Half( vector<int> vec, int & remainder ){
	        int carry = 0, tmp;
	        for(int i=0; i<vec.size(); i++ ){
	            tmp = (carry*10 + vec[i]) / 2;
	            carry = (carry*10 + vec[i]) % 2;
	            vec[i] = tmp;
	        }   
	        remainder = carry;
	        while( vec.size() > 0 && 0 == vec[0] )
	            vec.erase( vec.begin() );
	        return vec;
	    }   
	
	    int superPow(int a, vector<int>& b) {
	        if( 1 == b.size() && 1 == b[0] )        
	            return a % 1337;
	
	        int remainder = 0;
	        vector<int> half_b = Half( b, remainder );
	        long result = superPow( a, half_b );
	        if( 0 == remainder )
	            result = (result * result % 1337);
	        else if( 1 == remainder )
	            result = ((result * result % 1337) * a % 1337);
	        return result;
	    }   
	};


The time complexity of TLE Code is O(log2(b)). If recursive solution will TLE, we should make it a loop solution.

### 3. AC Code

(37 Lines)

	class Solution {
	public:
	    bool isMoreThanZero( vector<int> & vec )
	    {   
	        while( vec.size() > 0 && 0 == vec[0] )
	            vec.erase( vec.begin() );
	        if( vec.empty() )
	            return false;
	        else
	            return true;
	    }   
	
	    void Half( vector<int>& vec, int & remainder ){
	        int carry = 0;
	        for(int i=0; i<vec.size(); i++ ){
	            vec[i] += carry*10;
	            carry = vec[i] % 2;
	            vec[i] /= 2;; 
	        }   
	        remainder = carry;
	        while( vec.size() > 0 && 0 == vec[0] )
	            vec.erase( vec.begin() );
	    }   
	
	    int superPow(int a, vector<int>& b) {
	        long result = 1;
	        a = a % 1337;
	        while( isMoreThanZero(b) ){
	            int remainder = 0;
	            Half( b, remainder );
	            if( 1 == remainder )
	                result = result * a % 1337;
	            a = a * a % 1337;
	        }   
	        return result;
	    }   
	};

The solution above is not concise. It divides the power `b` half each time. Think about the fomular `a^(xy) mod c = (a^x)*(a^y) mod c`. Can we split the power `b` not by half but by its digits ? For example `a^(10x+y) mod c = a^(10x) * a^y mod c`. Now we get the formula:

	a^(10x+y) mod c = a^(10x) * a^y mod c
					= (a^10)^x * a^y mod c
					= [(a^10)^x mod c] * (a^y mod c) mod c

### 4. AC Solution

(20 Lines)

	class Solution {
	public:
	    int myPow( int x, int y ){
	        int ans = 1;
	        while( y-- )
	            ans = ans * x % 1337;
	        return ans;
	    }
	
	    int superPow(int a, vector<int>& b) {
	        a = a % 1337;
	        long result = 1;
	        while( b.size() ){
	            result = result * myPow( a, b.back() ) % 1337;
	            b.pop_back();
	            a = myPow( a, 10 );
	        }
	        return result;
	    }
	};

This algorithm is a.k.a. `Montgomery Modular Exponential`, which is the core algorithm in RSA. Yes, the man in the cover picture is [Perter Montgomery](https://en.wikipedia.org/wiki/Peter_Montgomery_(mathematician)).

### Reference

1. [Modular exponentiation. Wikipedia](https://en.wikipedia.org/wiki/Modular_exponentiation)
2. [Fast Exponentiation Algorithms. Programming Logic](http://www.programminglogic.com/fast-exponentiation-algorithms/)
3. [[leetcode] 372. Super Pow 解题报告. 小榕流光的专栏](http://blog.csdn.net/qq508618087/article/details/51860222)
