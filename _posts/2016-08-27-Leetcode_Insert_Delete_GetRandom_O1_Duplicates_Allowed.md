---
layout: post
title:  "Leetcode - 381. Insert Delete GetRandom O(1)"
date:   2016-08-27 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [380. Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/)

Design a data structure that supports all following operations in average O(1) time.

**Note: Duplicate elements are allowed.**

insert(val): Inserts an item val to the collection.
remove(val): Removes an item val from the collection if present.
getRandom: Returns a random element from current collection of elements. The probability of each element being returned is linearly related to the number of same value the collection contains.

**Example:**

	Init an empty collection.
	RandomizedCollection collection = new RandomizedCollection();
	
	// Inserts 1 to the collection. Returns true as the collection did not contain 1.
	collection.insert(1);
	
	// Inserts another 1 to the collection. Returns false as the collection contained 1. Collection now contains [1,1].
	collection.insert(1);
	
	// Inserts 2 to the collection, returns true. Collection now contains [1,1,2].
	collection.insert(2);
	
	// getRandom should return 1 with the probability 2/3, and returns 2 with the probability 1/3.
	collection.getRandom();
	
	// Removes 1 from the collection, returns true. Collection now contains [1,2].
	collection.remove(1);
	
	// getRandom should return 1 and 2 both equally likely.
	collection.getRandom();

### 1. Analyse

Same principle as [380. Insert Delete GetRandom O(1)](http://edlinlink.github.io/Leetcode_Insert_Delete_GetRandom_O1.html). But this time we store a struct into `unordered_map`.

### AC Code

	class RandomizedCollection {
	public:
	    vector<int> position;
	    unordered_map<int, unordered_set<int> > mp;
	    /** Initialize your data structure here. */
	    RandomizedCollection() {
	        srand(time(NULL));
	    }
	    
	    /** Inserts a value to the collection. Returns true if the collection did not already contain the specified element. */
	    bool insert(int val) {
	        mp[val].insert( position.size() );
	        position.push_back(val);
	        return 1 == mp[val].size();
	    }
	    
	    /** Removes a value from the collection. Returns true if the collection contained the specified element. */
	    bool remove(int val) {
	        if( 0 == mp.count(val) )    return false;
	
	        int index = *mp[val].begin();
	        int last_item = position.back();
	
	        position[ index ] = last_item;              //move the last item 
	        mp[val].erase( index );                     //remove val index
	        mp[last_item].insert( index );              //add last_item index
	        mp[last_item].erase( position.size()-1 );   //remove last_item index
	        position.pop_back();                        //remove the last item
	
	        if( mp[val].empty() )
	            mp.erase( val );                        //remove val index 
	        return true;
	    }
	    
	    /** Get a random element from the collection. */
	    int getRandom() {
	        return position[ rand() % position.size() ];
	    }
	}; 

### Reference 

1. [share my 116ms c++ solution. zoharlee.](https://discuss.leetcode.com/topic/54825/share-my-116ms-c-solution)
