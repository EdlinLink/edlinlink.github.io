---
layout: post
title:  "Single Linked List Reverse"
date:   2017-05-25 12:00:00
tags:	technology
myTag:	c/c++
headimg: demo.png

---

### Single Linked List Reverse

**Linked List Reverse** is a typical interview question to determine whether the interviewee understand linked-list data structure good enought or not. 

Here are two typical methods to reverse a linked-list. We can remember them in mind as a template to slove any linked list reverse problem.

---

### 0. Define the Node

	struct Node {
		int val;
		Node* next;
		Node(int x): val(x), next(NULL) {}
	};

---

### 1. Non-Recursion

	void reverse( Node * root )
	{
		Node * pLeft	= NULL;
		Node * pCurrent = root;
		Node * pRight	= NULL;

		while( pCurrent )				//Line 7
		{
			pRight = pCurrent->next;	//Line 9
			pCurrent->next = pLeft;		//Line 10
			pLeft = pCurrent;			//Line 11
			pCurrent = pRight;			//Line 12
		}

		return root = pLeft;
	}

Here are the analyse of the previous code above.

***Line 7:*** The initial state of linked list:

			   (root)
				Node0 ---->	Node1 ---->	Node2 ----> ...	----> NodeN ----> NULL
			  (pCurrent)

***Line 9:*** Get the next node of the current node:

			   (root)
				Node0 ----> Node1 ---->	Node2 ----> ...	----> NodeN ----> NULL
			  (pCurrent)   (pRight)

***Line 10:*** Reverse the 'next' pointer of current node and point to previous node:

			   (root)
	 NULL <----	Node0		Node1 ---->	Node2 ----> ...	----> NodeN ----> NULL
	(pLeft)	  (pCurrent)   (pRight)

***Line 11:*** Move previous node pointer to next node;

			   (root)
	 NULL <----	Node0		Node1 ---->	Node2 ----> ...	----> NodeN ----> NULL
			  (pCurrent)   (pRight)
			   (pLeft)

***Line 12:*** Move current node pointer to next node;

			   (root)
	 NULL <----	Node0		Node1 ---->	Node2 ----> ...	----> NodeN ----> NULL
			   (pLeft)	   (pCurrent)
							(pRight)
			   
***Conclusion: 4 Steps***

1. Tag the next node of Current node;
2. Reverse Current node Next pointer;
3. Move Previous node to its next one;
4. Move Current node to its next one;

---

### 2. Recursion

	Node * reverse( Node * root )
	{
		if( NULL == root || NULL == root->next )
			return root;							//Line 4

		Node * pLast= reverse( root->next );		//Line 6
		(root->next)->next = root;					//Line 7
		root->next = NULL;							//Line 8
		return pLast;								//Line 9
	}

***Line 4:*** Return last node;

				Node0 ---->	.....
							..... ---->	NodeL 
										NodeL ---->	NodeM
													NodeM ----> NodeN
																NodeN ----> NULL
															   (root)

***Line 6:*** Get the last node from its recursion layer;

				Node0 ----> ..... 	
							..... ----> NodeL	
										NodeL ---->	NodeM 
													NodeM ----> NodeN ----> NULL
												   (root)	   (pLast)

***Line 7:*** Reverse the 'next' pointer of the next node of current(root);

				Node0 ---->	.....
							..... ----> NodeL	
										NodeL ---->	NodeM
													NodeM <===> NodeN 
												   (root)	   (pLast)

***Line 8:*** Set NULL to the current node;

				Node0 ---->	.....
							..... ----> NodeL		 	
										NodeL ---->	NodeM <---- NodeN 
												(root)|		   (pLast)
													  V
													 NULL

***Line 9:*** Return the Last node to its upper layer.

				Node0 ---->	.....
							..... ----> NodeL ---->	NodeM <---- NodeN 
									   (root)		  |		   (pLast)
													  V
													 NULL

***Loop Line6~Line9:***
	
				Node0 <---- ..... <---- NodeL <---- NodeM <---- NodeN
			(root)|											   (pLast)
				  V	
				 NULL

***Conclusion: 2 steps***

1. Reverse the 'next' pointer of the next node of current;
2. Set NULL to the current node 'next' pointer;

---

Reference 

1. [单链表转置. skylinesky. CSDN Blog.](http://blog.csdn.net/skylinesky/article/details/7606944)
