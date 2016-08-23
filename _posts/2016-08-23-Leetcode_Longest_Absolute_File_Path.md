---
layout: post
title:  "Leetcode - 388. Longest Absolute File Path"
date:   2015-11-27 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [388. Longest Absolute File Path](https://leetcode.com/problems/longest-absolute-file-path/)


Suppose we abstract our file system by a string in the following manner:

The string `"dir\n\tsubdir1\n\tsubdir2\n\t\tfile.ext"` represents:

	dir
	    subdir1
	    subdir2
	        file.ext

The directory `dir` contains an empty sub-directory `subdir1` and a sub-directory `subdir2` containing a file `file.ext`.

The string `"dir\n\tsubdir1\n\t\tfile1.ext\n\t\tsubsubdir1\n\tsubdir2\n\t\tsubsubdir2\n\t\t\tfile2.ext"` represents:

	dir
	    subdir1
	        file1.ext
	        subsubdir1
	    subdir2
	        subsubdir2
	            file2.ext

The directory `dir` contains two sub-directories `subdir1` and `subdir2`. `subdir1` contains a file `file1.ext` and an empty second-level sub-directory `subsubdir1`. `subdir2` contains a second-level sub-directory `subsubdir2` containing a file `file2.ext`.

We are interested in finding the longest (number of characters) absolute path to a file within our file system. For example, in the second example above, the longest absolute path is `"dir/subdir2/subsubdir2/file2.ext"`, and its length is `32` (not including the double quotes).

Given a string representing the file system in the above format, return the length of the longest absolute path to file in the abstracted file system. If there is no file in the system, return `0`.

**Note:**

+ The name of a file contains at least a `.` and an extension.
+ The name of a directory or sub-directory will not contain a `.`.

Time complexity required: `O(n)` where `n` is the size of the input string.

Notice that a/aa/aaa/file1.txt is not the longest file path, if there is another path `aaaaaaaaaaaaaaaaaaaaa/sth.png`.

### 1. Analyse

This is another typical stack problem. Store the dir/file into stack. Pop the element when stack element is greater than the dir layers. Count the length when meets dir containing a dot. 

### 2. AC Code

(46 Lines)

	class Solution {
	public:
	    int lengthLongestPath(string input) {
	        vector<string> vec;
	        string dir;
	        int dirlevel = 0;
	        bool file = false;
	        int length = 0;
	
	        input.append("\n");
	        for( int i=0; i<input.size(); i++ ){
	            switch (input[i]){
	                case '\n':
	                    while( vec.size() > dirlevel )
	                        vec.pop_back();
	                    if( !dir.empty() ){
	                        vec.push_back( dir );
	                        dir = "";
	                    }
	                    if( file )
	                        length = max( length, getLength(vec) );
	
	                    file = false;
	                    dirlevel = 0;
	                    break;
	                case '\t':
	                    dirlevel++;
	                    break;
	                case '.':
	                    file = true;
	                default:
	                    dir += input[i];
	                    break;
	            }
	        }
	        return length;
	    }
	
	    int getLength( const vector<string> & vec ){
	        int length = 0;
	        for( int i=0; i<vec.size(); i++ )
	            length += vec[i].size();
	        length += ( max(0, (int)vec.size()-1) );
	        return length;
	    }
	};
