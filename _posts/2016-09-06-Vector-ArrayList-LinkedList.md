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

## Vector Class

The Vector Class is also dynamically grow-able and shrink-able collection of objects like an ArrayList class. But, the main difference between ArrayList and Vector is that **Vector class is synchronized**. That means, only one thread can enter into vector object at any moment of time.

Vector class has same features as ArrayList. Vector class also extends AbstractList class and implements List interface. It also implements 3 marker interfaces: RandomAccess, Cloneable and Serializable.

## Vector vs. ArrayList

**Thread Safety**: is the main difference between ArrayList and Vector class. ArrayList class is not thread-safe whereas Vector class is thread-safe. Vector class is a synchronized class. Only one thread can enter into Vector object at any moment of time during execution. Where as ArrayList class is not synchronized. Multiple threads can access ArrayList object simultaneously.

**Performance**: ArrayList has better performance compared to Vector. It is because, Vector class is synchronized. It makes the threads to wait for object lock to enter into vector object. Where as ArrayList class is not synchronized. Threads need not to wait for object lock to access ArrayList object. This makes ArrayList faster than the Vector class.

**Capacity Increment**: Whenever the size of the ArrayList exceeds it’s capacity, the capacity is increased by **half (50%)** of the current capacity. Where as in case of Vector, the capacity is increased by Capacity Increment passed while creating the Vector object. If Capacity increment is not passed, capacity will be **doubled** automatically when the size exceeds it’s capacity.

**Size**: You can manually change the current size of the vector. Vector class has a method called setSize(). Using this method, you can change the current size of the vector. If the new size is greater than the current size, new slots will be filled with null elements and if the new size is smaller than the current size, extra elements will be discarded. But in case of ArrayList, you can’t change the current size manually. It doesn’t have methods which alter it’s size. The size of the ArrayList will be changed only when you add or delete it’s elements.

**Traversing The Elements**: ArrayList elements can be traversed using Iterator, ListIterator and using either normal or advanced for loop. But, vector elements can be traversed using Enumeration also along with these methods. Vector class has a method called elements() which returns Enumeration object containing all elements of the vector. Where as ArrayList does not have such methods.

**Legacy Code**: Vector class is considered as Legacy code. Because, it exist in Java before the introduction of Collection Framework. Earlier it was not a part of Collections. Later it has been included in Collections. But, the older methods of vector class have been retained as it is.

## Why Not To Use Vector Class In Your Code?

Vector class is often considered as obsolete or “Due for Deprecation” by many experienced Java developers. They always recommend and advise not to use Vector class in your code. They prefer using ArrayList over Vector class. Some of the reasons are listed below.

**Thread Safety can be achieved without Vector**: Vector class has only one advantage over ArrayList i.e. it is thread safe. But, you can achieve thread safe ArrayList by using synchronizedList() method of Collections class.

```java
ArrayList<Integer> list = new ArrayList<Integer>();
Collections.synchronizedList(list);
```

**Thread Safeness of Vector class is time consuming**: All methods of Vector class are synchronized. This makes each and every operation on Vector object thread safe. But, it is time consuming. Because, you need to acquire object lock for each operation you want to perform on vector object.

**Enumeration vs. Iterator**: Vector class has a method which return Enumeration over the elements of Vector object. Although, Enumerations are faster than the Iterator, but it is not backed by the original collection. That means, any changes made to original collection does not reflect in Enumeration object. They ignore the modifications done during iteration. This may cause issues.

**Vector class is poorly designed**: Vector class combines two features: "Re-sizable Array" and "Synchronization". This makes poor design. Because, if you need just "Re-sizable Array" and you use Vector class for that, you will get "synchronized Resizable Array" not just re-sizable array. This may reduce the performance of your application. Therefore, instead of using Vector class, always use ArrayList class. You will have re-sizable array and whenever you want to make it synchronized, use Collections.SynchronizedList().

# LinkedList

## LinkedList Class

The **LinkedList** class in Java is an implementation of **doubly linked list** which can be used both as a **List** as well as **Queue**. The LinkedList in java can have any type of elements including null and duplicates. Elements can be inserted and can be removed from both the ends and can be retrieved from any arbitrary position.

The LinkedList class extends **AbstractSequentialList** and implements **List** and **Deque** interfaces. It also implements 2 marker interfaces: **Cloneable** and **Serializable**.

Elements in the LinkedList are called as **Nodes**. Where each node consist of three parts: Reference To Previous Element, Value Of The Element and Reference To Next Element. Reference To Previous Element of first node and Reference To Next Element of last node are null as there will be no elements before the first node and after the last node.

You can insert, remove, retrieve the elements at both the ends and also in the middle of the LinkedList. Insertion and removal operations in LinkedList are faster than the ArrayList. Because in LinkedList, there is no need to shift the elements after each insertion and removal. only references of next and previous elements need to be changed. 

Retrieval of the elements is very slow in LinkedList as compared to ArrayList. Because LinkedList is not of type Random Access. i.e. the elements can not be accessed randomly. To access the given element, you have to traverse the LinkedList from beginning or end (whichever is closer to the element) to reach the given element.

## LinkedList vs. ArrayList

Similarities Between ArrayList And LinkedList:
1. Both ArrayList and LinkedList implement **List interface**.
2. Both ArrayList and LinkedList are **Cloneable** and **Serializable**.
3. Both ArrayList and LinkedList maintain **insertion order**.
4. Both are **non synchronized**.

Differences Between ArrayList And LinkedList:

|                        | ArrayList                                | LinkedList                               |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Structure              | index-based array                        | doubly linked list                       |
| Insertion And Removal  | Very slow. Because after each insertion and removal, elements need to be shifted. O(n) | Faster than the ArrayList. Because there is no need to shift the elements after every insertion and removal. Only references of previous and next elements are to be changed. O(1) |
| Retrieval or Searching | Fast. Because all elements in ArrayList are index based. O(1) | Very slow. Because to retrieve an element, you have to traverse from beginning or end (Whichever is closer to that element) to reach that element. O(n) |
| Memory Occupation      | ArrayList requires less memory compared to LinkedList. Because ArrayList holds only actual data and it’s index. | LinkedList requires more memory compared to ArrayList. Because, each node in LinkedList holds data and reference to next and previous elements. |
| When To Use            | If your application does more retrieval than the insertions and deletions, then use ArrayList. | If your application does more insertions and deletions than the retrieval, then use LinkedList. |

# References

[1] Collection Framework List Interface: [http://javaconceptoftheday.com/collection-framework-list-interface](http://javaconceptoftheday.com/collection-framework-list-interface)

[2] ArrayList Java 实现: [http://www.cnblogs.com/heaven.kaixin/articles/2074745.html](http://www.cnblogs.com/heaven.kaixin/articles/2074745.html)

[3] ArrayList 和 Vector 以及 synchronizedList: [http://www.cnblogs.com/yanghuahui/p/3365976.html](http://www.cnblogs.com/yanghuahui/p/3365976.html)

[4] ArrayList vs LinkedList vs Vector: [http://www.cnblogs.com/chenpi/p/5505375.html](http://www.cnblogs.com/chenpi/p/5505375.html)

[5] ArrayList vs. LinkedList vs. Vector: [https://dzone.com/articles/arraylist-vs-linkedlist-vs](https://dzone.com/articles/arraylist-vs-linkedlist-vs)

[6] Difference between ArrayList, Vector and LinkedList in Java: [https://javabeat.net/difference-arraylist-vector-linkedlist-java](https://javabeat.net/difference-arraylist-vector-linkedlist-java)










