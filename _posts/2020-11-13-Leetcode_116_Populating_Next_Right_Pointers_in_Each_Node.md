---
layout: post
title:  "116. Populating Next Right Pointers in Each Node"
date:   2020-11-13 17:21:00
tags:	leetcode
myTag:	leetcode
headimg: demo.png

---

### [116. Populating Next Right Pointers in Each Node](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)

### Question

You are given a ***perfect binary tree*** where all leaves are on the same level, and every parent has two children. The binary tree has the following definition:

```
struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
```

Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to `NULL`.

Initially, all next pointers are set to `NULL`.

 

**Follow up:**

+ You may only use constant extra space.
+ Recursive approach is fine, you may assume implicit stack space does not count as extra space for this problem.
 

**Example 1:**

![](https://assets.leetcode.com/uploads/2019/02/14/116_sample.png)

```
Input: root = [1,2,3,4,5,6,7]
Output: [1,#,2,3,#,4,5,6,7,#]
Explanation: Given the above perfect binary tree (Figure A), your function should populate each next pointer to point to its next right node, just like in Figure B. The serialized output is in level order as connected by the next pointers, with '#' signifying the end of each level.
```

**Constraints:**

+ The number of nodes in the given tree is less than 4096.
+ -1000 <= node.val <= 1000



---
---

### Summary

It is an interesting problem. 

At the very beginning I did not notice the link between Node(5) and Node(6). So I'm assuming for each node, link:

+ node -> left -> next = node -> right;

After considering link between Node(5) and Node(6), we need to use the `next` pointer:

+ node -> right = node -> next -> left;

---
---

### Solution

```cpp
/*
// Definition for a Node.
class Node {
public:
    int val;
    Node* left;
    Node* right;
    Node* next;

    Node() : val(0), left(NULL), right(NULL), next(NULL) {}

    Node(int _val) : val(_val), left(NULL), right(NULL), next(NULL) {}

    Node(int _val, Node* _left, Node* _right, Node* _next)
        : val(_val), left(_left), right(_right), next(_next) {}
};
*/

class Solution {
public:
    Node* connect(Node* root) {
        if (!root)  
            return root;
        
        if (root->left) {
            root->left->next = root->right;
            
            if (root->next) {
                if (root->right) {
                    root->right->next = root->next->left;
                }
            }
            
            connect(root->left);
            connect(root->right);   
        }
     
        return root;
    }
};

```
