---
layout: post
title:  "47. Permutations II"
date:   2020-11-12 13:26:00
tags:	leetcode
myTag:	leetcode
headimg: demo.png

---

### [47. Permutations II](https://leetcode.com/problems/permutations-ii/)

### Question

Given a collection of numbers, nums, that might contain duplicates, return all possible unique permutations in any order.

**Example 1:**

```
Input: nums = [1,1,2]
Output:
[[1,1,2],
 [1,2,1],
 [2,1,1]]
```

**Example 2:**

``
Input: nums = [1,2,3]
Output: [[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

**Constraints:**

+ 1 <= nums.length <= 8
+ -10 <= nums[i] <= 10


---
---

### Summary

It is easy to come up an idea of `dfs` with `recursion` strategy. 

If there is only one num in vector, add it into result;
If there are two nums in vector, compute all permutations of the last num, and add the first num into different postion of the each permutation.

---
---

### Solution

```cpp
class Solution {
public:
    set<vector<int>> result;
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        result.clear();
        sort(nums.begin(), nums.end());
        dfs(nums);
        return vector<vector<int>>(result.begin(), result.end());
    }
    
    void dfs(const vector<int> vec){
        if (vec.size()==1){
            result.insert(vec); 
            return;
        }
        
        int num = vec[0]; 
        dfs(vector<int>(vec.begin()+1, vec.end()));
            
        set<vector<int>> tmp = result;
        result.clear();
            
        for(auto permutation : tmp) {
            for(int i=0; i<=permutation.size(); i++) {
                vector<int> candidate = permutation;
                candidate.insert(candidate.begin()+i, num);
                result.insert(candidate);
            }
        }
    }
};

```
