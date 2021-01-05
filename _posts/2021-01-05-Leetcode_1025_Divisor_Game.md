---
layout: post
title:  "1025. Divisor Game"
date:   2021-01-05 10:39:00
tags:	leetcode
myTag:	leetcode
headimg: demo.png

---

### [1025. Divisor Game](https://leetcode.com/problems/divisor-game/)

### Question

Alice and Bob take turns playing a game, with Alice starting first.

Initially, there is a number N on the chalkboard.  On each player's turn, that player makes a move consisting of:

+ Choosing any x with 0 < x < N and N % x == 0.
+ Replacing the number N on the chalkboard with N - x.

Also, if a player cannot make a move, they lose the game.

Return True if and only if Alice wins the game, assuming both players play optimally.

 

**Example 1:**

```
Input: 2
Output: true
Explanation: Alice chooses 1, and Bob has no more moves.
```

**Example 2:**

```
Input: 3
Output: false
Explanation: Alice chooses 1, Bob chooses 1, and Alice has no more moves.
```

**Note:**

+ 1 <= N <= 1000




---
---

### Summary

Given an `odd` number, after subtract a factor, it can only become an `even` number.  

Given an `even` number, after subtract a factor, it can become either `odd` or `even` number.

```
                +---+
                V   |
(ODD) <====> (EVEN) |
                |   |
                +---+
```

If we can finally make Alice to meet number `2` before her start, she will always win. So we need to make Bob always face `odd` number. 

Given an `even` number, Alice can make it `odd` and pending Bob to take number.
Given an `odd` number, Alice cannot make it `odd`.

---
---

### Solution

```cpp
class Solution {
public:
    bool divisorGame(int N) {
        return N % 2 == 0;
    }
};
```
