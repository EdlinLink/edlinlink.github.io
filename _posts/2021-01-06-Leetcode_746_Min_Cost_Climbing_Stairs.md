---
layout: post
title:  "746. Min Cost Climbing Stairs"
date:   2021-01-06 08:31:00
tags:	leetcode
myTag:	leetcode
headimg: demo.png

---

### [746. Min Cost Climbing Stairs](https://leetcode.com/problems/min-cost-climbing-stairs/)

### Question

On a staircase, the `i`-th step has some non-negative cost `cost[i]` assigned (0 indexed).

Once you pay the cost, you can either climb one or two steps. You need to find minimum cost to reach the top of the floor, and you can either start from the step with index 0, or the step with index 1.

**Example 1:**

```
Input: cost = [10, 15, 20]
Output: 15
Explanation: Cheapest is start on cost[1], pay that cost and go to the top.
```

**Example 2:**

```
Input: cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]
Output: 6
Explanation: Cheapest is start on cost[0], and only step on 1s, skipping cost[3].
```

**Note:**

1. cost will have a length in the range `[2, 1000]`.
2. Every cost[i] will be an integer in the range `[0, 999]`.

---
---

### Summary

You should notice that the `cost` are all staircases cost, the `top` is higher than the staircases. So in Example 1, there are 3 staircases and 1 top.

The solution will be simple DP to memorize the minimal cost reaching `staircase[i]`.

---
---

### Solution

```cpp
class Solution {
public:
    map<int, int> dp;
    int minCostClimbingStairs(vector<int>& cost) {
        return Cost(cost, cost.size());
    }
    
    int Cost(vector<int> & cost, int idx) {
        if (idx < 2)    
            return 0;
        
        if (dp.find(idx) != dp.end())
            return dp[idx];
        
        dp[idx] = min( Cost(cost, idx-1)+cost[idx-1], Cost(cost,idx-2)+cost[idx-2] );
        return dp[idx];
    }
    
};
```
