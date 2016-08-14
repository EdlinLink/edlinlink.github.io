---
layout: post
title:  "Leetcode - Reverse Linked List"
date:   2016-08-14 12:00:00
tags:	technology
myTag:	algorithm

---

### [206. Reverse Linked List](https://leetcode.com/problems/reverse-linked-list/)

Reverse a singly linked list.

### 1. Analyse

First, we give a diagram of a linked list.

		Node0---->Node1---->Node2...
	
If we want to reverse the pointer of Node1, what is the procedure?

1. current pointer points to Node1.

		Node0---->Node1---->Node2...
					A
					|
				  p_cur

2. post pointer points to Node2.

		Node0---->Node1---->Node2...
					A		  A
					|		  |
				  p_cur		p_post

3. pre pointer points to Node0.

		Node0---->Node1---->Node2...
		  A			A		  A
		  |			|		  |
		p_pre	  p_cur		p_post
	
4. break pointer of Node1.

		Node0---->Node1--X->Node2...
		  A			A		  A
		  |			|		  |
		p_pre	  p_cur		p_post
	
5. let pointer of Node1 points to Node0.

		Node0<--->Node1		Node2...
		  A			A		  A
		  |			|		  |
		p_pre	  p_cur		p_post

It is clear that if we want to reverse the pointer of a current node, we need pointers point to its pre/post nodes.

### 2. Handle The First Node

Each node has a post node(the last node points to NULL). But not every node has its pre node(the fist node does not have). We'd better add a dump node for the first node so that the whole procedure is probably the same.

		NodeDump---->Node0---->Node1---->Node2...

### 3. AC code

(Line: 25)

	class Solution {
	public:
	    ListNode* reverseList(ListNode* head) {
	        ListNode *dump = new ListNode(0);
	        dump->next = head;
	
	        ListNode *p_pre = dump;
	        ListNode *p_cur = head;
	        ListNode *tail = head;
	
	        while( p_cur ){
	            ListNode *p_post = p_cur->next;
	            p_cur->next = p_pre;
	                
	            p_pre = p_cur;
	            tail = p_cur;
	            p_cur = p_post;
	        }   
	
	        if(head)	head->next = NULL;
	        delete dump;
	            
	        return tail;
	    }   
	};

### Encouraged

Linked list problem is one of my hatest problem. But if we can consider it step by step, then we can still solve it.
