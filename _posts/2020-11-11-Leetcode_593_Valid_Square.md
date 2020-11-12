---
layout: post
title:  "593. Valid Square"
date:   2020-11-11 22:50:00
tags:	leetcode
myTag:	leetcode
headimg: demo.png

---

### [593. Valid Square](https://leetcode.com/problems/valid-square/)

### Question

Given the coordinates of four points in 2D space, return whether the four points could construct a square.

The coordinate (x,y) of a point is represented by an integer array with two integers.

**Example:**

```
Input: p1 = [0,0], p2 = [1,1], p3 = [1,0], p4 = [0,1]
Output: True
```

**Note:**

1. All the input integers are in the range [-10000, 10000].
2. A valid square has four equal sides with positive length and four equal angles (90-degree angles).
3. Input points have no order.


---
---

### Summary

If a quadrilateral is a square, it must meet 2 requirements:

+ It is a rectangle. (The central point to all four points have equal length);
+ Two adjacent edges have same length;

There is one test case we need to pay attention:

***If there is any two points has the same position, it can't be a square.***

---
---

### Solution

```cpp
class Solution {
public:
    bool validSquare(vector<int>& p1, vector<int>& p2, vector<int>& p3, vector<int>& p4) {
        const double x = (p1[0]+p2[0]+p3[0]+p4[0]) / 4.0;
        const double y = (p1[1]+p2[1]+p3[1]+p4[1]) / 4.0;

        double d1 = distance(p1[0], p1[1], x, y);
        double d2 = distance(p2[0], p2[1], x, y);
        double d3 = distance(p3[0], p3[1], x, y);
        double d4 = distance(p4[0], p4[1], x, y);
        bool isRectangle = equal(d1,d2) && equal(d3,d4) && equal(d1,d3);

        vector<double> distOfP1;
        distOfP1.push_back(distance(p1[0], p1[1], p2[0], p2[1]));
        distOfP1.push_back(distance(p1[0], p1[1], p3[0], p3[1]));
        distOfP1.push_back(distance(p1[0], p1[1], p4[0], p4[1]));
        sort(distOfP1.begin(), distOfP1.end());
        bool sameLength = equal(distOfP1[0], distOfP1[1]) && !equal(distOfP1[0], 0);

        return isRectangle & sameLength;
    }

    double distance(const double x1, const double y1, const double x2, const double y2) {
        return sqrt((x1-x2)*(x1-x2) + (y1-y2)*(y1-y2));
    }

    bool equal(const double x1, const double x2) {
        return (x1>=x2 && x1-x2 <0.0000000001) || (x1<x2 && x2-x1<0.0000000001);
    }
};
```
