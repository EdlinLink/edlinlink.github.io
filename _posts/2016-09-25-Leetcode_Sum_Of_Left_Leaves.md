---
layout: post
title:  "Leetcode - 404. Sum of Left Leaves"
date:   2016-09-23 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [404. Sum of Left Leaves](https://leetcode.com/problems/sum-of-left-leaves/)

Find the sum of all left leaves in a given binary tree.

Example:

		3
	   / \
	  9  20
		/  \
	   15   7

	There are two left leaves in the binary tree, with values 9 and 15 respectively. Return 24.

### 1. Analyse

Resently, the problem in Leetcode is easier than before. Recursion to solve this problem. 

Please pay attention, just sum the left LEAVES.

### 2. AC Code

(15 Lines)

	/**
	 * Definition for a binary tree node.
	 * struct TreeNode {
	 *     int val;
	 *     TreeNode *left;
	 *     TreeNode *right;
	 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
	 * };
	 */
	class Solution {
	public:
	    int sumOfLeftLeaves(TreeNode* root) {
			return sumLeft( root, false );
	    }

		int sumLeft( TreeNode* root, bool isLeft ){
			if( !root )	
				return 0;
			else if( !root->left && !root->right && isLeft )
				return root->val;
			else
				return sumLeft( root->left, true ) + sumLeft( root->right, false );
		}
	};

