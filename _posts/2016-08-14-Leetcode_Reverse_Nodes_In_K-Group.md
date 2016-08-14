---
layout: post
title:  "Leetcode - 25. Reverse Nodes in k-Group"
date:   2016-08-14 12:00:00
tags:	technology
myTag:	algorithm

---

### [25. Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)

Given a linked list, reverse the nodes of a linked list k at a time and return its modified list.

If the number of nodes is not a multiple of k then left-out nodes in the end should remain as it is.

You may not alter the values in the nodes, only nodes itself may be changed.

Only constant memory is allowed.

For example,

Given this linked list: 1->2->3->4->5

For k = 2, you should return: 2->1->4->3->5

For k = 3, you should return: 3->2->1->4->5

### 1. Analyse

Let's review a easier problem fisrt [Reverse Linked List](http://edlinlink.github.io/Leetcode_Reverse_Linked_List.html). If we now already implemented a reverse function, it will be easier to solve the current problem.

Now if we have a linked list: 1->2->3->4->5->6->7->8 and k = 3, our target is: 3->2->1->6->5->4->7->8. We reverse the whole multiple k linked list first. 

	1->2->3->4->5->6->7->8	(origin)
	8->7->6->5->4->3->2->1	(reverse)

We now split the node into groups

	(8)->(7)->(6,5,4)->(3,2,1)

What will happen if we reverse the groups?

	(3,2,1)->(6,5,4)->(7)->(8)

We reach our target.

### 2. AC code

(Line 49)

	class Solution {
	public:
	    int count;
	    ListNode* reverseKGroup(ListNode* head, int k) {
	        count = 0;
	        head = reverseLinkedList( head, count, 0, 0 );  
	        head = reverseLinkedList( head, count, count, k );  
	        return head;
	    }   
	
	    ListNode* moveForward(ListNode* head, int k){ 
	        count += k;
	        while( k-- )
	            head = head->next;
	        return head;
	    }   
	
	    ListNode * reverseLinkedList( ListNode * head, int & count, int total, const int & k ){
	        count = 0;
	        ListNode * dump = new ListNode(0);
	        dump->next = head;
	
	        ListNode *p_pre = dump;
	        ListNode *p_cur = head;
	        ListNode *tail= head;
	        while( p_cur ){
	            count++;
	            tail= p_cur;
	
	            if( k>0 && total && total % k == 0 )
	                p_cur = moveForward( p_cur, k-1 );
	            else
	                total--;
	
	            ListNode *p_post = p_cur->next;
	            p_cur->next = p_pre;	
	
	            p_pre = tail;
	            p_cur = p_post;
	        }
	
	        if( k>0 && count && count % k == 0 )
	            head = moveForward( head, k-1 );
	
	        if( head )  head->next = NULL;
	        delete dump;
	        return tail;
	    }   
	};


