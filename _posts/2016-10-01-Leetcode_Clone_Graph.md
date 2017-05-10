---
layout: post
title:  "Leetcode - 133. Clone Graph"
date:   2016-10-01 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [133. Clone Graph](https://leetcode.com/problems/clone-graph/)

Clone an undirected graph. Each node in the graph contains a label and a list of its neighbors.


OJ's undirected graph serialization:
Nodes are labeled uniquely.

We use # as a separator for each node, and , as a separator for node label and each neighbor of the node.
As an example, consider the serialized graph {0,1,2#1,2#2,2}.

The graph has a total of three nodes, and therefore contains three parts as separated by #.

First node is labeled as 0. Connect node 0 to both nodes 1 and 2.
Second node is labeled as 1. Connect node 1 to node 2.
Third node is labeled as 2. Connect node 2 to node 2 (itself), thus forming a self-cycle.
Visually, the graph looks like the following:

       1
      / \
     /   \
    0 --- 2
         / \
         \_/

### 1. Analyse

When we know it is a graph problem, we have solved half of it. We can use a `map` to store the node, use a `set` to store the visit node.
 
### 2. AC Code

	class Solution {
	public:
	    UndirectedGraphNode *cloneGraph(UndirectedGraphNode *node) {
			if( !node ) return node;
			set<int> visit;
			queue<UndirectedGraphNode*> que;
			map<int,UndirectedGraphNode*> graph;			 
			
			que.push(node);
			graph[node->label] = new UndirectedGraphNode(node->label);
			visit.insert(node->label);
			while( !que.empty() ){
				UndirectedGraphNode* node = que.front();	
				que.pop();

				for( int i=0; i<node->neighbors.size(); i++ ){
					if( 0 == visit.count(node->neighbors[i]->label ) ){
						graph[node->neighbors[i]->label] = new UndirectedGraphNode(node->neighbors[i]->label);
						visit.insert(node->neighbors[i]->label);
						que.push(node->neighbors[i]);
					}
					graph[node->label]->neighbors.push_back( graph[node->neighbors[i]->label] );
				}
			}
			return graph[node->label];
	    }
	};

