---
layout: post
title:  "Reservoir Sampling"
date:   2014-12-11 12:00:00
tags:	tech
myTag:	algorithm

---

# Reservoir Sampling

----------------------

***Question: 给定一个Streaming的Data，未知长度，要求在Streaming结束后返回N个Data，且是等概率的。***

这是今天在[知乎](http://www.zhihu.com/question/26934313#answer-9361603)上看到的问题。很有趣，大三同学去面腾讯实习的时候也听到过这道题的变体。原来这道题是有名字的。解决这个问题的算法叫做Reservoir Sampling，中文直译叫"蓄水池抽样"。

---------------------------------------------------------------------------

### 算法概述

为什么叫做Reservoir呢? 原因很简单，因为这个算法的核心就是有新的Data进来替换掉旧的Data，就跟蓄水池放水加水一样。

首先保留前N个数据，当第i个数据来到时，以概率N/i决定是否保留这个数据，如果保留的话，就在前面N个数据中随机剔除一个。最终当所有数据过了一遍，返回保留的N个数据即可。

---------------------------------------------------------------------------

### 算法证明

证明算法可行的关键就是如果决定保留新来的数据，那么没有被剔除数据的概率是不是和这个新的数据的概率一致呢。我们这样思考

对于前N个数据，它们被保留的概率是1。对于第i=N+1的数据，存在两种情况，它被保留(P=N/(N+1))和它没有被保留(1/(N+1))。对于这两种情况，没有被剔除的N-1个数据的概率是:

	P(j) = P(j|not_keep_i) * P(not_keep_i) + P(j|keep_i) * P(keep_i) 
	P = 1 * 1/(N+1)  +  (1 - 1/N) * (N/(N+1))
	  = N/(N+1)

被留下来的数据的概率是N/(N+1)和新保留的数据概率一致(P=保留数/当前遍历数)。

当i为大于N+1后面的数时，同理，没有被剔除的N-1个数据的概率是:

	P(j) = P(j|not_keep_i) * P(not_keep_i) + P(j|keep_i) * P(keep_i) 
	P = N/(i-1) * (1 - N/i) + N/(i-1)*(1 - 1/N) * N/i
	  = N/i

没有被剔除的N-1个数据的概率为N/i与新来被保留的数据的概率一致，所以算法正确性得证。

---------------------------------------------------------------------------

### 编程实现

伪代码描述

	array R[k];
	integer i, j;

	for each i in 1 to k do
		R[i] = S[i]
	done;

	for each j in k+1 to length(S) do
		j = random(i, i);
		if j <= k then
			R[j] = S[j]
		fi
	done

--------------------------------------------------------------------------

### 参考资料

1. [随机抽样问题(reservoir sampling)的证明. 新浪博客](http://blog.sina.com.cn/s/blog_48e3f9cd01019jyr.html)
2. [有哪些算法惊艳到了你? 知乎](http://www.zhihu.com/question/26934313#answer-9361603)
3. [Reservoir sampling. Wikipedia](http://en.wikipedia.org/wiki/Reservoir_sampling)
