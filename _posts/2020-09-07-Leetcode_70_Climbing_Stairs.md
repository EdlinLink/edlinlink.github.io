---
layout: post
title:  "70. Climbing Stairs"
date:   2020-09-07 12:00:00
tags:	leetcode
myTag:	
headimg: demo.png

---

### [https://leetcode.com/problems/climbing-stairs/](https://leetcode.com/problems/climbing-stairs/)

### Question

You are climbing a stair case. It takes n steps to reach to the top.

Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

**Example 1:**

```
Input: 2
Output: 2
Explanation: There are two ways to climb to the top.
1. 1 step + 1 step
2. 2 steps
```

**Example 2:**

```
Input: 3
Output: 3
Explanation: There are three ways to climb to the top.
1. 1 step + 1 step + 1 step
2. 1 step + 2 steps
3. 2 steps + 1 step
```

**Constraints:**

+ 1 <= n <= 45

---

### Summary

It is a simple DP. There is two ways to climb to stair `n`, 1) from stair `n-1` climb 1 step or 2) from stair `n-2` climb 2 steps.

	climbStairs(n) = climbStairs(n-1) + climbStairs(n-2);

---

### Solution

{% highlight cpp linenos %}
{% raw %}
class Solution {
public:
    map<int,int> memo = {{0,0},{1,1}, {2,2}};
    int climbStairs(int n) {
        if (memo.find(n) != memo.end())
            return memo[n];
        else
            memo[n] = climbStairs(n-1) + climbStairs(n-2);
        return memo[n];
    }
};
{% endraw %}
{% endhighlight %}

---
