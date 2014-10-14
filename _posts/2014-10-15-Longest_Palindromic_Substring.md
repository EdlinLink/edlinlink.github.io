---
layout: post
title:  "Longest Palindromic Substring"
date:   2014-10-15 12:00:00
tags:	tech
myTag:	algorithm

---

# Longest Palindromic Substring

---------------------------------------------------------------------------

最长回文子串。回文就是顺着读倒着读都是同一个顺序的序列，例如"aba", "abba"它们都是回文。那么最长回文子串的问题就是给定一个字符串，找出其中最长的回文子串。

我们有不少方法解决这个问题，但是时间复杂度都很高。这里介绍一个叫Manacher's Algorithm的方法。它的时间复杂度是O(n)。看到O(n)的算法，实在是太佩服了，而且算法也不难。下面是Manacher's Algorithm的介绍。

## Manacher's Algorithm

回文子串有一个稍微麻烦的地方就是判断字符串是奇数个字符还是偶数个字符。假设现在输入的字符串str是"abaaba"，我们先预处理这个字符串，讲每一个字符间加入一个标记字符"#"，使得整个字符串str变成"#a#b#a#a#b#a#"，长度为13，现在我们有一个长度为13的数组P，用P[i]记录以str[i]为对称中心的回文的长度是多少，例如:

	str		#	a	#	b	#	a	#	a	#	b	#	a	#
	P		1	2	1	4	1	2	7	2	1	4	1	2	1	

之后我们只需要遍历一次数组P就可以知道最长回文大小了。

现在的问题是我们怎么计算数组P。

-------------------------

我们从str[0]开始，以str[0]为回文的对称中心，判断str[0+1]和str[0-1]是否相等。当然str[0-1]是越界了。为了方便起见，我们预处理输入字符串str时，实际上在最开头需要加另一个标记字符"$"，使得处理后的字符串是

	str		$	#	a	#	b	#	a	#	a	#	b	#	a	#
	P		0	1	2	1	4	1	2	7	2	1	4	1	2	1	
	
那么我们从str[1]开始，以str[1]为对称中心判断str[1]前后的字符是否相等，相等的话P[1]就+1，直到不相等为止。此时P[1]记录了以str[1]为对称中心的回文能向右推讲的长度。然后我们处理str[2]...，一直处理完最后一个字符。

可能有的同学会问，这样时间复杂度不是O(n^2)吗？是的，现在这个算法的时间复杂度是O(n^2)。我们现在要对这个基本算法做点处理，让它时间复杂度变成O(n)。

我们需要用到两个变量，boundary和axis。boundary表示当前记录的回文能向右最远到达的地方，axis记录最长回文的对称中心。

	for(int i=1; i<n; i++){
		
		if(boundary > i)
			p[i] = min(p[2*axis-i], boundary-i);

		while(str[i+p[i]] == str[i-p[i]])
			p[i]++;

		if(i + P[i] > boundary){
			boundary = i + P[i];
			axis = i;
		}
	}

核心代码只有这么短，现在就来解释一下究竟它在干什么。

假设现在的情况是
	
	          2                    9     11    13                   20
			  L					   i'	 C     i                    R
		      |                    |     |     |                    |
	T =	#  b  #  a  #  b  #  c  #  b  #  a  #  b  #  c  #  b  #  a  #  c  #  c  #  b  #  a
	P = 1  2  1  4  1  2  1  8  1  2  1  10 1  ?

当前boundary在R(Right)的位置，axis在C(Center)的位置，即str[2:20]关于str[11]对称。2*axis-i实际上是i关于axis对称的位置，即i'。现在需要计算P[i]。

	          2                    9     11    13                   20
			  L					   i'	 C     i                    R
		      |                 ---+---  |  ---+---                 |
	T =	#  b  #  a  #  b  #  c  #  b  #  a  #  b  #  c  #  b  #  a  #  c  #  c  #  b  #  a
	P = 1  2  1  4  1  2  1  8  1  2  1  10 1  ?

由于str[2:20]是关于str[11]对称的，那么P[i]的值应该也是关于str[11]对称的，也即是P[i] = P[i'] = P[2*axis-i]。

我们继续往下做，当前情况是

	          2              7           11          15             20
			  L				 i'	   	     C           i              R
		------|--------------+-----------|---------
		                        ---------|-----------+--------------| - - -
	T =	#  b  #  a  #  b  #  c  #  b  #  a  #  b  #  c  #  b  #  a  #  c  #  c  #  b  #  a
	P = 1  2  1  4  1  2  1  8  1  2  1  10 1  2  1  ?

现nacher算法--O（n）回文子串算法在我们需要计算P[i]，我们只知道str[2:20]是关于str[11]对称。由于str[0:14]关于str[7]对称(由P[7]可知)，所以更有str[2:12]关于str[7]对称。所以str[10:20]一定是关于str[15]对称的，所以P[i] = boundary - i。接着我们需要继续看str[21]和str[10]是不是相同的，如果相同P[i]还要继续+1，直到不相等。需要注意的是因为我们还没有扫描到str[20]之后的数据，所以我们不能说str[8:22]关于str[15]对称。我们只能说str[10:20]关于str[15]对称。

-------------------------------

现在回到我们的代码上去。计算P[i]时，当boundary > i的时候，我们需要选择P[2*axis-i]和boundary-i较小的值初始化P[i]。这样我们就节省掉P[i]从0开始增长到当前值的过程。

现在这个就是Manacher's Algorithm。不过你可能现在还会认为这是个时间复杂度O(n^2)的算法。因为for循环里面还有一个while循环。但是实际不是这样子的。每执行一次for循环，我们的P[i]如果P[i+P[i]]和P[i-P[i]]相等，那么将把boundary向右推进一步，boundary只能最多增加N步；如果P[i+P[i]]和P[i-P[i]]不相等，那么它只做了比较是否相等这一步，时间复杂度为O(1)。所以总的时间复杂度只有O(n)。

现在你可以尝试去做做Leetcode的[Longest Palindromic Substring ](https://oj.leetcode.com/problems/longest-palindromic-substring/)这道题目了。

## Reference

1. [Longest Palindromic Substring Part II](http://leetcode.com/2011/11/longest-palindromic-substring-part-ii.html)
2. [Manacher算法 -- O(n)回文子串算法](http://blog.csdn.net/ggggiqnypgjg/article/details/6645824)





