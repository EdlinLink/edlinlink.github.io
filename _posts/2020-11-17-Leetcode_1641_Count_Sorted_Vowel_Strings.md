---
layout: post
title:  "1641. Count Sorted Vowel Strings"
date:   2020-11-17 22:45:00
tags:	leetcode
myTag:	leetcode
headimg: demo.png

---

### [1641. Count Sorted Vowel Strings](https://leetcode.com/problems/count-sorted-vowel-strings/)

### Question

Given an integer `n`, return *the number of strings of length* `n` *that consist only of vowels (`a`, `e`, `i`, `o`, `u`) and* *are* ***lexicographically sorted***.

A string `s` is **lexicographically sorted** if for all valid `i`, `s[i]` is the same as or comes before `s[i+1]` in the alphabet.

**Example 1:**

```
Input: n = 1
Output: 5
Explanation: The 5 sorted strings that consist of vowels only are ["a","e","i","o","u"].
```

**Example 2:**

```
Input: n = 2
Output: 15
Explanation: The 15 sorted strings that consist of vowels only are
["aa","ae","ai","ao","au","ee","ei","eo","eu","ii","io","iu","oo","ou","uu"].
Note that "ea" is not a valid string since 'e' comes after 'a' in the alphabet.
```

**Example 3:**

```
Input: n = 33
Output: 66045
``` 

**Constraints:**

+ `1 <= n <= 50`

---
---

### Summary

Find the pattern:

+ When `n=1`: 
  - "a", "e", "i", "o", "u"; 

+ When `n=2`: 
  - "u" can be added before n=1 "u" start string ("uu");
  - "o" can be added before n=1 "o", "u" start string ("oo", "ou");
  - "i" can be added before n=1 "i", "o", "u" start string ("ii", "io", "iu");
  - "e" can be added before n=1 "e", "i", "o", "u" start string ("ee", "ei", "eo", "eu")
  - "a" can be added before n=1 "a", "e", "i", "o", "u" start string ("aa", "ae", "ai", "ao", "au")

+ When `n=3`: 
  - "u" can be added before n=2 "u" start string ("uuu");
  - "o" can be added before n=2 "o", "u" start string ("ooo", "oou", "ouu")
  - "i" can be added before n=2 "i", "o", "u" start string ("iii", "iio", "iiu", "ioo", "iou", "iuu")
  - "e" can be added before n=2 "e", "i", "o", "u" start string ("eee", "eei", "eeo", "eeu", "eii", "eio", "eiu", "eoo", "eou", "euu")
  - "a" can be added before n=2 "a", "e", "i", "o", "u" start string ("aaa", "aae", "aai", "aao", "aau", "aee", "aei", "aeo", "aeu", "aii", "aio", "aiu", "aoo", "aou", "auu")

...

+ When `n=N`: 
  - "u" can be added before n=N-1 "u" start string; 
    * total(n=N, u) = total(n=N-1, u);
  - "o" can be added before n=N-1 "o", "u" start string; 
    * total(n=N, o) = total(n=N-1, o) + total(n=N-1, u);
  - "i" can be added before n=N-1 "i", "o", "u" start string; 
    * total(n=N, i) = total(n=N-1, i) + total(n=N-1, o) + total(n=N-1, u);
  - "e" can be added before n=N-1 "e", "i", "o", "u" start string; 
    * total(n=N, e) = total(n=N-1, e) + total(n=N-1, i) + total(n=N-1, o) + total(n=N-1, u);
  - "a" can be added before n=N-1 "a", "e", "i", "o", "u" start string; 
    * total(n=N, a) = total(n=N-1, a) + total(n=N-1, e) + total(n=N-1, i) + total(n=N-1, o) + total(n=N-1, u);

---
---

### Solution

```cpp
class Solution {
public:
    int countVowelStrings(int n) {
        int a=1, e=1, i=1, o=1, u=1;
        while (--n) {
            u = u;
            o += u;
            i += o;
            e += i;
            a += e;
        }
        return a+e+i+o+u;
    }
};
```
