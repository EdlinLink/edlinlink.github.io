---
layout: post
title:  "938 Range Sum of BST"
date:   2020-11-15 19:40:00
tags:	leetcode
myTag:	leetcode
headimg: demo.png

---

### [938. Range Sum of BST](https://leetcode.com/problems/range-sum-of-bst/)

### Question

Given the `root` node of a binary search tree, return the sum of values of all nodes with a value in the range `[low, high]`.

*Example 1:*

![](https://assets.leetcode.com/uploads/2020/11/05/bst1.jpg)

```
Input: root = [10,5,15,3,7,null,18], low = 7, high = 15
Output: 32
```

*Example 2:*

![](https://assets.leetcode.com/uploads/2020/11/05/bst2.jpg)

```
Input: root = [10,5,15,3,7,13,18,1,null,6], low = 6, high = 10
Output: 23
```

*Constraints:*

+ The number of nodes in the tree is in the range `[1, 2 * 10^4]`.
+ `1 <= Node.val <= 10^5`
+ `1 <= low <= high <= 10^5`
+ All `Node.val` are *unique*.


---
---

### Summary

Recursion traversal the binary tree, and sum up the value between *low* and *high*.

---
---

### Solution

```cpp
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    
    int rangeSumBST(TreeNode* root, int low, int high) {
        int sum = 0;
        if (root) {
            if (root->val >= low && root->val <= high){
                sum += root->val;
                sum += rangeSumBST(root->right, low, high);
                sum += rangeSumBST(root->left, low, high);
            }
            if (root->val < low)
                sum += rangeSumBST(root->right, low, high);
            if (root->val > high)
                sum += rangeSumBST(root->left, low, high);
        }
        return sum;
    }
};
```
