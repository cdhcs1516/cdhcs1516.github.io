---
layout:     post
title:      Memory Computaion
subtitle:   
date:       2019-07-31
author:     cdh
header-img: img/post-bg-course.jpg
catalog: true
tags:
    - Coursera
    - Algorithm
    - Memory
---

## Typical memory usage for primitive types and arrays

| Type       | bytes |
| :--------: | :---: |
| boolean    | 1 |
| byte       | 1 |
| char       | 2 |
| int        | 4 |
| float      | 4 |
| long       | 8 |
| double     | 8 |
| char[]     | 2N+24 |
| int[]      | 4N+24 |
| double[]   | 8N+24 |
| char[][]   | ~2MN |
| int[][]    | ~4MN |
| double[][] | ~8MN |

## Typical memory usage for objects in Java

***Object overhead.*** 16 bytes.

***Reference.*** 8 bytes.

***Padding.*** Each object uses a multiple of 8 bytes.

## Examples for memory computaion

![Example 1](https://github.com/cdhcs1516/cdhcs1516.github.io/raw/master/img/memory-ex1.png "ex1")
![Example 2](https://github.com/cdhcs1516/cdhcs1516.github.io/raw/master/img/memory-ex2.png "ex2")