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

The binary search tree or red black tree can also be used as the data structure of the index file, but why we don't use them in a real database system?

The time is very long when find data from a disk. To reduce the times (frequency) of disk I/O, system will read a nearby data in advance (pre-read), the size of the pre-read data usually equals to integral multiples of **page** (4k), which is the basic unit of data that is exchanged between RAM and disk.

For B-Tree, finding a key will at most search **h** (the height of the tree) nodes. And the size of every node equals to one **page**, therefore loading one node just need one I/O time.

**one node -> one page -> one I/O**.

In real database system, the **d** of the B-Tree is very large, that means the **h** will be very small, for example **3**, thus finding a data will at most cost **3** I/O times.

That is the reason why we don't use red black tree as the data structure of the index file. The height of red black tree is large, that means much more times of I/O to disk. The child and parent nodes may not be placed in one physical **page**, so it can't take the advantage of the power of **pre-read**.

# Implementation Of MySQL

## MyISAM

B+Tree:

- **key** of leaf node: stores the column data
- **data** of leaf node: stores the **physical address** 

non-clustered (非聚集索引), index files are separated from the data file.

## InnoDB

B+Tree:

- **key** of leaf node: stores the primary key of the table
- **data** of leaf node: stores the whole rest of the columns of data

It is clustered (聚集索引), the index file for primary key is the data file itself. 

The **data** of leaf node in other non-primary key index file, stores the **primary keys** in main index file, rather than **physical addresses**. Therefore, searching data from non-primary key column will firstly search the index file for this column, then the primary key index file.

Please note that:

- 不建议使用过长的字段作为主键，因为所有辅助索引都引用主索引，过长的主索引会令辅助索引变得过大
- 不建议使用非单调的字段做主键，它会使B+Tree频繁分裂调整，降低效率，推荐使用自增字段

# Index Optimization

## Composite Index & Leftmost Prefixing

联合索引 & 最左前缀原理

## Index Selectivity & Prefix Index

索引的选择性（Selectivity），指不重复的索引值（也叫基数，Cardinality）与表记录数（#T）的比值：

> Index Selectivity = Cardinality / #T
>
> SELECT count(DISTINCT(title))/count(\*) AS Selectivity FROM employees.titles;

选择性的取值范围为(0, 1]，选择性越高的索引价值越大，这是由B+Tree的性质决定的。选择性低的字段不建议建立索引。

**前缀索引**：取某字段的前几个字符建立索引，兼顾**索引大小**和**选择性**。

> ALTER TABLE employees.employees
>
> ADD INDEX `first_name_last_name4` (first_name, last_name(4));

## Optimization Of InnoDB's Primary Key

在使用InnoDB存储引擎时，如果没有特别的需要，请永远使用一个与业务无关的自增字段作为主键。使用自增主键，每次插入新的记录时，记录就会顺序添加到当前索引节点的后续位置，当一页写满，就会自动开辟一个新的页。这样就会形成一个紧凑的索引结构，近似顺序填满。由于每次插入时也不需要移动已有数据，因此效率很高，也不会增加很多开销在维护索引上。

如果使用非自增主键（如果身份证号或学号等），由于每次插入主键的值近似于随机，因此每次新纪录都要被插到现有索引页得中间某个位置，时MySQL不得不为了将新记录插到合适位置而移动数据，甚至目标页面可能已经被回写到磁盘上而从缓存中清掉，此时又要从磁盘上读回来，这增加了很多开销，同时频繁的移动、分页操作造成了大量的碎片，得到了不够紧凑的索引结构。

# References

[1] MySQL索引背后的数据结构及算法原理: [http://blog.codinglabs.org/articles/theory-of-mysql-index.html](http://blog.codinglabs.org/articles/theory-of-mysql-index.html)






