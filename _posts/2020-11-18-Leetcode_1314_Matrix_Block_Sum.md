---
layout: post
title:  "1314. Matrix Block Sum"
date:   2020-11-18 00:13:00
tags:	leetcode
myTag:	leetcode
headimg: demo.png

---

### [1314. Matrix Block Sum](https://leetcode.com/problems/matrix-block-sum/)

### Question

Given a `m * n` matrix mat and an integer `K`, return a matrix `answer` where each `answer[i][j]` is the sum of all elements `mat[r][c]` for `i - K <= r <= i + K, j - K <= c <= j + K`, and `(r, c)` is a valid position in the matrix.


**Example 1:**

```
Input: mat = [[1,2,3],[4,5,6],[7,8,9]], K = 1
Output: [[12,21,16],[27,45,33],[24,39,28]]
```

**Example 2:**

```
Input: mat = [[1,2,3],[4,5,6],[7,8,9]], K = 2
Output: [[45,45,45],[45,45,45],[45,45,45]]
```

**Constraints:**

+ m == mat.length
+ n == mat[i].length
+ 1 <= m, n, K <= 100
+ 1 <= mat[i][j] <= 100

---
---

### Summary

To calculate the sum of a matrix, we need to construct the "accumulate sum matrix".

example given matrix:

```
[[1,2,3],
 [4,5,6],
 [7,8,9]]
```

construct the accumulate sum matrix, sum up all value of on the left side and upper side of position `[i][j]`.

```
[[1,3,6],
 [5,12,21],
 [12,27,45],
]]
```

The `answer` of position `[i][j]` will be:

```
answer[i][j] = sumMat[i+K][j+K] - sumMat[i-K-1][j+K] - sumMat[i+K][j-K-1] + sumMat[i-K-1][j-K-1];
```

---
---

### Solution

```cpp
class Solution {
public:
    vector<vector<int>> matrixBlockSum(vector<vector<int>>& mat, int K) {
        const int ROW = mat.size(), COL = mat[0].size();
        vector<vector<int>> sumMat = mat;
        vector<vector<int>> answer = vector<vector<int>>(ROW, vector<int>(COL, 0));

        for (int i=0; i<ROW; i++)
            for (int j=1; j<COL; j++)
                sumMat[i][j] += sumMat[i][j-1];

        for (int j=0; j<COL; j++)
            for (int i=1; i<ROW; i++)
                sumMat[i][j] += sumMat[i-1][j];

        for (int i=0; i<ROW; i++) {
            for (int j=0; j<COL; j++) {
                const int row = min(ROW-1,i+K), col = min(COL-1,j+K);
                answer[i][j] = sumMat[row][col];
                if (i-K-1>=0)
                    answer[i][j] -= sumMat[i-K-1][col];
                if (j-K-1>=0)
                    answer[i][j] -= sumMat[row][j-K-1];
                if (i-K-1>=0 && j-K-1>=0)
                    answer[i][j] += sumMat[i-K-1][j-K-1];
            }
        }
        return answer;
    }
};
```
