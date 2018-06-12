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

11,4

# Vector

14,15,16

# LinkedList

17,20

Xxxxx

# References

[1] xxxxxx: []()

[2] xxxxxx: []()

[3] xxxxxx: []()

[4] xxxxxx: []()













