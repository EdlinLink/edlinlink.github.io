---
layout: post
title:  "63. Unique Paths II"
date:   2020-09-12 23:48:00
tags:	leetcode
myTag:	leetcode
headimg: demo.png

---

### [63. Unique Paths II](https://leetcode.com/problems/unique-paths-ii/)

### Question

A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).

The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).

Now consider if some obstacles are added to the grids. How many unique paths would there be?

![](https://assets.leetcode.com/uploads/2018/10/22/robot_maze.png)

An obstacle and empty space is marked as 1 and 0 respectively in the grid.

**Note**: m and n will be at most 100.

**Example 1:**

```
Input:
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
Output: 2
Explanation:
There is one obstacle in the middle of the 3x3 grid above.
There are two ways to reach the bottom-right corner:
1. Right -> Right -> Down -> Down
2. Down -> Down -> Right -> Right
```

---
---

### Summary

This is a follow up problem of [62. Unique Paths](https://leetcode.com/problems/unique-paths/)

Current position(m,n) paths equal to position(m,n-1) + position(m-1,n) paths;

    position(m,n) = position(m,n-1) + position(m-1,n);

If `position(m,n-1)` or `position(m-1, n)` has obstacle, then don't count it.

There is a special case here:

If the final position(m,n) has obstacle, then the initial `path = 0`.

---
---

### Solution

```cpp
class Solution {
public:
    map<pair<int,int>, int> memo;
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid[0].size()-1;
        int n = obstacleGrid.size()-1;
        memo[make_pair(0,0)] = (obstacleGrid[n][m] == 0 ? 1 : 0);
        return dp(m, n, obstacleGrid);
    }

    int dp(int col, int row, vector<vector<int>>& obstacleGrid) {
        if (memo.find(make_pair(col,row)) != memo.end())
            return memo[make_pair(col,row)];

        int path = 0;
        if (col > 0 && obstacleGrid[row][col-1] != 1)
            path += dp(col-1, row, obstacleGrid);
        if (row > 0 && obstacleGrid[row-1][col] != 1)
            path += dp(col, row-1, obstacleGrid);

        memo[make_pair(col,row)] = path;
        return path;
    }
};
```
