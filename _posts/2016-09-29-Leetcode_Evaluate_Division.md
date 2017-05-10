---
layout: post
title:  "Leetcode - 399. Evaluate Division"
date:   2016-09-29 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [399. Evaluate Division](https://leetcode.com/problems/evaluate-division/)

Equations are given in the format A / B = k, where A and B are variables represented as strings, and k is a real number (floating point number). Given some queries, return the answers. If the answer does not exist, return -1.0.

Example:
Given a / b = 2.0, b / c = 3.0. 
queries are: a / c = ?, b / a = ?, a / e = ?, a / a = ?, x / x = ? . 
return [6.0, 0.5, -1.0, 1.0, -1.0 ].

The input is: vector<pair<string, string>> equations, vector<double>& values, vector<pair<string, string>> queries , where equations.size() == values.size(), and the values are positive. This represents the equations. Return vector<double>.

According to the example above:

	equations = [ ["a", "b"], ["b", "c"] ],
	values = [2.0, 3.0],
	queries = [ ["a", "c"], ["b", "a"], ["a", "e"], ["a", "a"], ["x", "x"] ]. 

The input is always valid. You may assume that evaluating the queries will result in no division by zero and there is no contradiction.

### 1. Analyse

It seems to be graph problem. Each variable is a node. Using BFS to solve this problem.

### 2. AC Code

(54 Lines)

    class Solution {
    public:
        struct Node{
            string name;
            double rate;
            Node(string n, double r) : name(n), rate(r) {};
        };
        vector<double> calcEquation(    vector<pair<string, string>> equations, 
                                        vector<double>& values, 
                                        vector<pair<string, string>> queries ) {
        
            map< string, vector<Node> > graph;
            for( int i=0; i<equations.size(); i++ ){
                string a = equations[i].first;
                string b = equations[i].second;
                double value = values[i];

                graph[a].push_back( Node(a,1));
                graph[a].push_back( Node(b,value) );
                if( value != 0 ){
                    graph[b].push_back( Node(a,1/value) );
                    graph[b].push_back( Node(b,1) );
                }
            }

            vector<double> ret;
            for( int i=0; i<queries.size(); i++ ){
                string from = queries[i].first;
                string dest = queries[i].second;
                set<string> visit;
                queue<Node> que;    Node start(from,1);
                que.push( start );
                bool found = false;
                while( !que.empty() && !found ){
                    Node node = que.front();
                    que.pop();
                    for( int j=0; j<graph[node.name].size() && !found; j++ ){
                        if( visit.count( graph[node.name][j].name ) == 0 ){
                            visit.insert( graph[node.name][j].name );
                            que.push( Node(graph[node.name][j].name, node.rate * graph[node.name][j].rate) );
                        }   
                        if( dest == graph[node.name][j].name )
                        {   
                            ret.push_back( node.rate * graph[node.name][j].rate );
                            found = true;
                        }   
                    }   
                }   
                if( !found )
                    ret.push_back(-1.00000);
            }   
            return ret;
        }
    };
