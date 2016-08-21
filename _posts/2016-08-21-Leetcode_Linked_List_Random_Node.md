---
layout: post
title:  "Leetcode - 382. Linked List Random Node"
date:   2016-08-21 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.jpg

---

### [382. Linked List Random Node](https://leetcode.com/problems/linked-list-random-node/)

Given a singly linked list, return a random node's value from the linked list. Each node must have the same probability of being chosen.

**Follow up:**
What if the linked list is extremely large and its length is unknown to you? Could you solve this efficiently without using extra space?

**Example:**

	// Init a singly linked list [1,2,3].
	ListNode head = new ListNode(1);
	head.next = new ListNode(2);
	head.next.next = new ListNode(3);
	Solution solution = new Solution(head);
	
	// getRandom() should return either 1, 2, or 3 randomly. Each element should have equal probability of returning.
	solution.getRandom();

### 1. Analyse

It is a typical problem call "Reservoir Sampling". If the length of array is N, the procedure of "Reservoir Sampling Algorhtm" is:

	1.	Pick the 1st element as the result.
	2.	1/2 possibility to change the result to be the 2nd element.
	3.	1/3 possibility to change the result to be the 3rd element.
	4.	1/4 possibility to change the result to be the 4th element.
	...
	N.	1/N possibility to change the result to be the Nth element.

The possibility of each element is:

	1st:	1*(1-1/2)*(1-1/3)*(1-1/4)*...*(1-1/N) = 1/N
	2nd:	1 * 1/2 * (1-1/3)*(1-1/4)*...*(1-1/N) = 1/N
	3rd:	1*(1-1/2) * 1/3 * (1-1/4)*...*(1-1/N) + 1*1/2 * 1/3 * (1-1/4)*...*(1-1/N)
			= [ 1*(1-1/2) + 1*1/2 ] * 1/3 * (1-1/4)*...*(1-1/N)
			= 1/N
	4th:	= 1/N
	...
	Nth		= 1/N

### 2. AC code

(21 Lines)

	/**
	 * Definition for singly-linked list.
	 * struct ListNode {
	 *     int val;
	 *     ListNode *next;
	 *     ListNode(int x) : val(x), next(NULL) {}
	 * };
	 */
	class Solution {
	public:
	    /** @param head The linked list's head.
	        Note that the head is guaranteed to be not null, so it contains at least one node. */
		ListNode *head;
	    Solution(ListNode* head) {	
			srand(time(NULL));
	        this->head = head;
	    }
	    
	    /** Returns a random node's value. */
	    int getRandom() {
			ListNode* p_node = this->head;
			int ans;
			int count = 1;
			while( p_node ){
				if( rand() % count++ == 0 )
					ans = p_node->val;
				p_node = p_node->next;
			}
			return ans;
	    }
	};
	
	/**
	 * Your Solution object will be instantiated and called as such:
	 * Solution obj = new Solution(head);
	 * int param_1 = obj.getRandom();
	 */

