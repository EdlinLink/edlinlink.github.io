---
layout: post
title:  "62. Unique Paths"
date:   2020-09-12 23:00:00
tags:	leetcode
myTag:	leetcode
headimg: demo.png

---

### [62. Unique Paths](https://leetcode.com/problems/unique-paths/)

### Question

A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

How many possible unique paths are there?

![](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)

**Example 1:**

```
Input: m = 3, n = 2
Output: 3
Explanation:
From the top-left corner, there are a total of 3 ways to reach the bottom-right corner:
1. Right -> Right -> Down
2. Right -> Down -> Right
3. Down -> Right -> Right
```

**Example 2:**

```
Input: m = 7, n = 3
Output: 28
```

**Constraints:**

+ 1 <= m, n <= 100
+ It's guaranteed that the answer will be less than or equal to 2 * 10 ^ 9.

---
---

### Summary

This is a DP problem. 

Current position(m,n) paths equal to position(m,n-1) + position(m-1,n) paths;

    position(m,n) = position(m,n-1) + position(m-1,n);

Initial state `postion(1,1) = 1`.

---
---

### Solution

```cpp
class Solution {
public:
    map<pair<int,int>, int> memo = { {make_pair(1,1),1} };
    int uniquePaths(int m, int n) {
        if (memo.find(make_pair(m, n)) != memo.end() ) {
            return memo[make_pair(m, n)];
        }

        int path = 0;
        if (m == 1)
            path = uniquePaths(m, n-1);
        else if (n == 1)
            path = uniquePaths(m-1, n);
        else
            path = uniquePaths(m-1, n) + uniquePaths(m, n-1);

        memo[make_pair(m, n)] = path;
        return path;
    }
};
```
