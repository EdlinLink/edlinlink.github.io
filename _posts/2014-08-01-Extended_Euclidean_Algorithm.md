---
layout: post
title:  "扩展欧几里得算法"
date:   2014-08-01 12:00:00
tags:	tech
myTag:	algorithm

---

# 扩展欧几里得算法

---------------------------

### 欧几里得算法

欧几里得算法也称为辗转相除法，这个是CS本科大一就会提到的算法。求a、b两个整数的最大公约数。

辗转相除法的公式表述是:

	gcd(a,b) = gcd(b,a mod b)

代码如下:

``` c++

// a > b
int gcd(int a, int b){
	if(b==0)
		return a;
	else
		return gcd(b, a%b);
}

```

证明如下:

	设 a = kb + r
	则 r = a mod b
	假设d为a、b的一个公约数
	则 d|a, d|b
	因为 r = a - kb
	所以 r|d
	所以 d是(b,a mod b)的一个公约数
	因此 (a,b)和(b,a mod b)的公约数是一样的
	所以 (a,b)的最大公约数也是(b,a mod b)的最大公约数

-------------------------

### 扩展算法

对于不完全为0的非负整数对a、b，gcd(a,b)表示a、b的最大公约数，必存在整数对x、y，使得 gcd(a,b)=ax+by。

证明如下:

	设 a>b
	1) 当b=0时，gcd(a,b)=a，此时x=1, y=0;
	2) 当a*b!=0时，
	设 ax1 + by1 = gcd(a,b); bx2 + (a mod b)y2 = gcd(b, a mod b)
	根据朴素欧几里得算法有
	ax1 + by1 = bx2 + (a mod b)y2
	即 ax1 + by1 = bx2 + (a-[a/b]*b)y2
	即 ax1 + by1 = ay2 + b(x2-[a/b]y2)
	根据恒等定理
	x1=y2; y1=(x2-[a/b]y2)
	上面的思想是以递归定义的，因为gcd不断的递归求解一定会有b=0的时候，所以递归可以结束。

代码如下:

``` c++
int exGcd(int a,int b,int &x,int &y){
	if(b==0){
		x=1;y=0;
		return a;
	}
	int r=exGcd(b,a%b,x,y);
	int t=x;
	x=y;
	y=t-a/b*y;
	return r;
}

```

-------------------------

到此为止，我们得到了不定式ax+by=gcd(a,b)的一组解(x,y)。对于一般的不等式ax+by=c，它的解显而易见应该是(x/gcd(a,b)\*c, y/gcd(a,b)\*c)。


-------------------------

### 参考资料

1. [扩展欧几里得算法. 百度百科](http://baike.baidu.com/view/1478219.htm)
