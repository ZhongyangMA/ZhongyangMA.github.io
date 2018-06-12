---
layout: post
title: "Vector, ArrayList and LinkedList"
date: 2016-09-06 10:22:18
categories: Java
permalink: /archivers/Vector-ArrayList-LinkedList
---

An ordered or sequential collection of objects can be represented by the List interface, which has a set of methods to store and manipulate the ordered collection of objects. ArrayList, Vector and LinkedList are some examples of List implementations. Lists allow us to insert or remove an element at any specific index. In this reading note, I will make a brief introduction to and comparison between Vector, ArrayList and LinkedList.

<!--more-->

# Collection

## Class Hierarchy

All classes and interfaces related to Collection Framework are placed in **java.util** package. **java.util.Collection** class is at the top of class hierarchy of Collection Framework. The figure below shows the hierarchy of collection framework.

![collection-hierarchy](https://github.com/ZhongyangMA/images/raw/master/list-interface/collection-hierarchy.png)

The entire collection framework is divided into four interfaces:
1. **List:** It handles sequential list of objects. **ArrayList**, **Vector** and **LinkedList** classes implement this interface.
2. **Queue:** It handles special list of objects in which elements are removed only from the head. **LinkedList** and **PriorityQueue** classes implement this interface.
3. **Set:** It handles list of objects which must contain unique element. This interface is implemented by **HashSet** and **LinkedHashSet** classes and extended by **SortedSet** interface which in turn, is implemented by **TreeSet**.
4. **Map:** This is the one interface in Collection Framework which is not inherited from Collection interface. It handles group of objects as Key-Value pairs. It is implemented by **HashMap** and **HashTable** classes and extended by **SortedMap** interface which in turn is implemented by **TreeMap**.

Three of above interfaces (List, Queue and Set) inherit from Collection interface. Although, Map is included in collection framework it does not inherit from Collection interface.

## List Interface

List Interface represents an ordered or sequential collection of objects. This interface has some methods which can be used to store and manipulate the ordered collection of objects. The classes which implement the List interface are called as Lists. ArrayList, Vector and LinkedList are some examples of lists. You have the control over where to insert an element and from where to remove an element in the list.

Here are some properties of lists:

1. Elements of the lists are ordered using Zero based index.
2. You can access the elements of lists using an integer index.
3. Elements can be inserted at a specific position using integer index. Any pre-existing elements at or beyond that position are shifted right.
4. Elements can be removed from a specific position. The elements beyond that position are shifted left.
5. A list may contain duplicate elements.
6. A list may contain multiple null elements.

# ArrayList

ArrayList can be defined as re-sizable array. ArrayList is same like normal array but it can grow and shrink dynamically to hold any number of elements. ArrayList is a sequential collection of objects which increases or decreases in size as we add or delete the elements.

In ArrayList, elements are positioned according to **Zero-based index**. That means, elements are inserted from index 0. **Default initial capacity** of an ArrayList is **10**. This capacity increases by **50%** automatically each time when the number of elements exceeds the old capacity (by copying the elements to a new Array). You can also specify initial capacity of an ArrayList while creating it.

ArrayList class implements **List interface** and extends **AbstractList**. It also implements 3 marker interfaces: **RandomAccess**, **Cloneable** and **Serializable**.

Some properties of ArrayList:

1. ArrayList can have any number of null elements.
2. ArrayList can have duplicate elements.
3. As ArrayList implements RandomAccess, you can get, set, insert and remove elements of the ArrayList from any arbitrary position.
4. When you insert an element in the middle of the ArrayList by **add** method, the elements at the right side of that position are shifted one position right, whereas using **set** method will replace the value at that index. When you **delete** an element, they will be shifted one position left. This feature of the ArrayList causes some performance issues as shifting of elements is time consuming if ArrayList has lots of elements.
5. ArrayList is **not synchronized**. That means, multiple threads can use same ArrayList simultaneously.
6. If you know the element, you can retrieve the position of that element.

# Vector

14,15,16

# LinkedList

17,20

Xxxxx

# References

[1] Collection Framework List Interface: [http://javaconceptoftheday.com/collection-framework-list-interface](http://javaconceptoftheday.com/collection-framework-list-interface)

[2] ArrayList Java 实现: [http://www.cnblogs.com/heaven.kaixin/articles/2074745.html](http://www.cnblogs.com/heaven.kaixin/articles/2074745.html)

[3] xxxxxx: []()

[4] xxxxxx: []()













