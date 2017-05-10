---
layout: post
title:  "Leetcode - 417. Pacific Atlantic Water Flow"
date:   2016-10-09 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [417. Pacific Atlantic Water Flow](https://leetcode.com/problems/pacific-atlantic-water-flow/)

Given an m x n matrix of non-negative integers representing the height of each unit cell in a continent, the "Pacific ocean" touches the left and top edges of the matrix and the "Atlantic ocean" touches the right and bottom edges.

Water can only flow in four directions (up, down, left, or right) from a cell to another one with height equal or lower.

Find the list of grid coordinates where water can flow to both the Pacific and Atlantic ocean.

Note:

1. The order of returned grid coordinates does not matter.
2. Both m and n are less than 150.

Example:

	Given the following 5x5 matrix:
	
	  Pacific ~   ~   ~   ~   ~ 
	       ~  1   2   2   3  (5) *
	       ~  3   2   3  (4) (4) *
	       ~  2   4  (5)  3   1  *
	       ~ (6) (7)  1   4   5  *
	       ~ (5)  1   1   2   4  *
	          *   *   *   *   * Atlantic
	
	Return:
	
	[[0, 4], [1, 3], [1, 4], [2, 2], [3, 0], [3, 1], [4, 0]] (positions with parentheses in above matrix).

### 1. Analyse

Flow from the left top and flow from the right bottom. If a grid can get both flow, that will be the result.

### 2. AC Code

(65 Lines)

    class Solution {
    public:
        vector<pair<int, int> > pacificAtlantic(vector<vector<int> >& matrix) {
            vector<pair<int, int> > ret;
            if( 0 == matrix.size() || 0 == matrix[0].size() )   return ret;
            int height = matrix.size(), width = matrix[0].size();
            vector<vector<bool> > P(matrix.size(), vector<bool>(matrix[0].size(), false) );
            vector<vector<bool> > A(matrix.size(), vector<bool>(matrix[0].size(), false) );
            int H[4] = {0,-1,0,1}, W[4] = {1,0,-1,0};
            stack<pair<int,int> > sta;
            for( int i=0; i<height; i++ ){
                P[i][0] = true;
                sta.push( pair<int,int>(i,0) );
            }
            for( int i=0; i<width; i++ ){
                P[0][i] = true;
                sta.push( pair<int,int>(0,i) );
            }
            while( !sta.empty() ){
                pair<int,int> item = sta.top();
                sta.pop();
                for( int i=0; i<4; i++ ){
                    if( 0 <= item.first+H[i] && item.first+H[i] < height && 
                        0 <= item.second+W[i] && item.second+W[i] < width &&
                        false == P[item.first+H[i]][item.second+W[i]] &&
                        matrix[item.first+H[i]][item.second+W[i]] >= matrix[item.first][item.second] ) 
                    {
                        P[item.first+H[i]][item.second+W[i]] = true;
                        sta.push(pair<int,int>(item.first+H[i], item.second+W[i]));
                    }
                } 
            }
             
            for( int i=0; i<height; i++ ){
                A[i][width-1] = true;
                sta.push( pair<int,int>(i,width-1) );
            }
            for( int i=0; i<width; i++ ){
                A[height-1][i] = true;
                sta.push( pair<int,int>(height-1,i) );
            }
            while( !sta.empty() ){
                pair<int,int> item = sta.top();
                cout << item.first <<" "<<item.second;
                sta.pop();
                for( int i=0; i<4; i++ ){
                    if( 0 <= item.first+H[i] && item.first+H[i] < height && 
                        0 <= item.second+W[i] && item.second+W[i] < width &&
                        false == A[item.first+H[i]][item.second+W[i]] && 
                        matrix[item.first+H[i]][item.second+W[i]] >= matrix[item.first][item.second] ) 
                    {
                        A[item.first+H[i]][item.second+W[i]] = true;
                        sta.push(pair<int,int>(item.first+H[i], item.second+W[i]));
                    }
                }
            }

            for( int i=0; i<height; i++ )
                for( int j=0; j<width; j++ )
                    if(P[i][j] && A[i][j])
                        ret.push_back( pair<int,int>(i,j) );
            return ret;
        }
    };
