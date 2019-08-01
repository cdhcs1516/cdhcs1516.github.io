---
layout:     post
title:      LeetCode N-Sum
subtitle:   
date:       2019-07-29
author:     cdh
header-img: img/post-bg-github-cup.jpg
catalog: true
tags:
    - Coding
    - Sum
---

### Introduction

N-Sum is a very popular type of algorithm problems, including [2-Sum](https://leetcode.com/problems/two-sum/), [3-Sum](https://leetcode.com/problems/3sum/), [4-Sum](https://leetcode.com/problems/4sum/),  [4-Sum II](https://leetcode.com/problems/4sum-ii/), etc. These problems requires us to find K elements in one or several arrays to guarentee the sum of those elements equals the target.

### Solution I. Brute

Walk through every element K times to check if their sum equals the target. This method is very time-consuming, time complexity is $O(N^K)$.

### Solution II. Binary Search

Use binary search method to walk through the array. But it needs to sort the arrays ahead. It can reduce the time complexity of **2-Sum** to $O(N \log N)$, **3-Sum** and **4-Sum** to $O(N^2 \log N)$.

### Solution III. HashMap

Take advantage of the quick find property of HashMap, we can still reduce the time complexity. But it also has the limitation that the elements of arrays should be distinct.