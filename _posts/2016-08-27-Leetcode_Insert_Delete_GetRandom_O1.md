---
layout: post
title:  "Leetcode - 380. Insert Delete GetRandom O(1)"
date:   2016-08-27 12:00:00
tags:	technology
myTag:	algorithm
headimg: demo.png

---

### [380. Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/)

Design a data structure that supports all following operations in average O(1) time.

insert(val): Inserts an item val to the set if not already present.
remove(val): Removes an item val from the set if present.
getRandom: Returns a random element from current set of elements. Each element must have the **same probability** of being returned.

**Example:**

	// Init an empty set.
	RandomizedSet randomSet = new RandomizedSet();
	
	// Inserts 1 to the set. Returns true as 1 was inserted successfully.
	randomSet.insert(1);
	
	// Returns false as 2 does not exist in the set.
	randomSet.remove(2);
	
	// Inserts 2 to the set, returns true. Set now contains [1,2].
	randomSet.insert(2);
	
	// getRandom should return either 1 or 2 randomly.
	randomSet.getRandom();
	
	// Removes 1 from the set, returns true. Set now contains [2].
	randomSet.remove(1);
	
	// 2 was already in the set, so return false.
	randomSet.insert(2);
	
	// Since 1 is the only number in the set, getRandom always return 1.
	randomSet.getRandom();

### 1. Analyse

O(1) insert and remove is easy, we can use a `set`. But `getRandom` is not O(1) in `set`. `getRandom` is O(1) in `vector`, but `remove` is not O(1) in `vector`.

If we use `vector` as the data structure, `getRandom` in O(1) is easy. `Insert` in O(1) is easy. How can we do `remove` in O(1)? 

The reason why `remove` in O(1) is hard is that we don't konw the target position. If we store the position information, we know the target position. But `remove` operation in vecotor will move all the elements after the target. But why don't we swap the element to the end of the vector? :)

### 2. AC Code

(32 Lines && 12 Comments)

	class RandomizedSet {
	public:
		vector<int> vec;
		unordered_map<int, int> mp;

	    /** Initialize your data structure here. */
	    RandomizedSet() {
			srand(time(NULL));
		}
	    
	    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
	    bool insert(int val) {
			if( mp.find(val) == mp.end() ){
				mp[val] = vec.size();
				vec.push_back( val );
				return true;
			}
			return false;
	    }
	    
	    /** Removes a value from the set. Returns true if the set contained the specified element. */
	    bool remove(int val) {
			if( vec.size() > 0 && mp.find(val) != mp.end() ){
				vec[mp[val]] = vec.back();
				mp[vec.back()] = mp[val];
				vec.pop_back();
				mp.erase(val);
				return true;
			}
			return false;
	    }
	    
	    /** Get a random element from the set. */
	    int getRandom() {
			return vec[rand() % vec.size()];
	    }
	};
	
	/**
	 * Your RandomizedSet object will be instantiated and called as such:
	 * RandomizedSet obj = new RandomizedSet();
	 * bool param_1 = obj.insert(val);
	 * bool param_2 = obj.remove(val);
	 * int param_3 = obj.getRandom();
	 */
	
### Reference

1. [Insert Delete GetRandom O(1) 常数时间内插入删除和获得随机数. Grandyang](http://www.cnblogs.com/grandyang/p/5740864.html)
	
