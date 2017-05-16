---
layout: post
title:  "Int + Unsigned Int"
date:   2017-05-17 12:00:00
tags:	technology
myTag:	c/c++
headimg: demo.png

---

### Int + Unsigned Int

Let's see some example code first.

**Example 1**

	int main()
	{
		unsigned int X = 10;
		int Y = 0;
		
		if( X > Y )	cout << "X >  Y" << endl;
		else		cout << "X <= Y" << endl;

		return 0;
	}

Output

	X >  Y

**Example 2**

	int main()
	{
		unsigned int X = 10;
		int Y = -1;
		
		if( X > Y )	cout << "X >  Y" << endl;
		else		cout << "X <= Y" << endl;

		return 0;
	}
	
Output

	X <= Y

Isn't `X` always larger than `Y` in these two cases?

---

### 1. Analyse

Two different type variable addition, will lead to ***implicit type conversion***. 

**`unsigned int` + `int` --> `unsigned int`**

Look back to the examples:

	X(10) > Y(0) 
	==> (unsigned int)X(10) > (unsigned int)Y(0) 
	==> true

	X(10) > Y(-1) 
	==> (unsigned int)X(10) > (unsinged int)Y(-1) 
	==> (unsigned int)X(10) > (unsinged int)Y(4294967295) 
	==> false

---
### 2. Trap

Why I write this artical? Because we will fell into this trap if we are not carefully enough. Look at the following code:

	int main()
	{
		cout << "Start Program" << endl;

		string str("hello world");
		for( int i=-1; i<str.size()-1; i++ )	
			cout << str[i+1];

		cout << "End Program" << endl;
		return 0;
	}

Output

	Start Program
	End Program

Because `string::size()` will return `size_t` which is `unsigned int`. It is equal to example 1 in the previous. 

We should pay more attension to the different data type operation.
