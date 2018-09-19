---
layout: post
title: "The Principle Of MySQL Index"
date: 2017-05-23 17:34:08
categories: MySQL
permalink: /archivers/The-Principle-Of-MySQL-Index
---

Recent days, I read a very excellent overview article, which gives us a deep insight about the algorithm and data structure behind MySQL index. Thanks to the author for introducing us about the index mechanism of database system. In this post, I recorded down the key information of the author's article for further review.

<!--more-->

# Data Structure Of Index

## B-Trees

Index is a kind of data structure, which helps us search specific data efficiently.

**B-Tree**: 

- d: degree, d > 1, maximum number of child node
- h: height, h > 0, the height of the tree
- non-leaf node: with *n* pointers and *n-1* keys, d<=n<=2d
- leaf node: contains at least 1 key and 2 pointers, at most 2d-1 keys and 2d pointers
- all leaf nodes have the same height, which equals the height of the tree
- keys have the ascending order within one node
- keys and pointers are placed alternatively, all the children at which a pointer points should great than the pointer's left key, and less than the pointer's right key

Due to the nature of B-Tree, searching data is very intuitive: we start binary search on its root node, return the data if we find it, otherwise we will do the binary search on its children nodes recursively. The average time complexity is O(logN).

**B+Tree**:

- non-leaf node doesn't store data, only keys
- leaf node doesn't store pointers, only keys and data

**Linked B+Tree**:

- every leaf node points to the neighbor leaf node, for the purpose of increasing interval access performance

## Why Using B-Trees

xxxxxx

xxxxxx

# Implementation Of MySQL

## MyISAM

xxxxx 

## InnoDB

xxxxx

# Index Optimization

xxxxx

# References

[1] MySQL索引背后的数据结构及算法原理: [http://blog.codinglabs.org/articles/theory-of-mysql-index.html](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)






