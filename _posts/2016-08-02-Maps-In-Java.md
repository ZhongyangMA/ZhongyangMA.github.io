---
layout: post
title: "Maps In Java"
date: 2016-08-02 11:21:23
categories: Java
permalink: /archivers/Maps-In-Java
---

Map is a data structure and it is mainly used for fast data lookups or searching. The **Map** interface in java is one of the four top level interfaces of Java Collection Framework along with **List**, **Set** and **Queue** interfaces. But, unlike others, it doesn’t inherit from Collection interface. Instead it starts its own interface hierarchy for maintaining the key-value associations. Map is an object of key-value pairs where each key is associated with a value. **HashMap**, **LinkedHashMap**, **ConcurrentHashMap** and **TreeMap** are four popular implementations of Map interface. In this article, we will discuss about the hierarchy, property and internal mechanisms of Map in Java.

<!--more-->

# Hierarchy of Maps

The Map interface doesn't inherit from Collection interface, it starts its own hierarchy for maintaining the key-value associations. **HashMap**, **LinkedHashMap**, **ConcurrentHashMap** and **TreeMap** are four popular implementations of Map interface. 

The picture below shows the class hierarchy structures of Map interface:

![https://github.com/ZhongyangMA/images/raw/master/maps-in-java/hierarchy-of-maps.png](https://github.com/ZhongyangMA/images/raw/master/maps-in-java/hierarchy-of-maps.png)

Note:

1. **(C)** means class (blue), whereas **(I)** means interface (orange).
2. The solid line means "extends", whereas the dashed line means "implements".

# How HashMap Works Internally

If your application demands fast insertion and retrieval, then **HashMap** is the ultimate choice. HashMap is the most used data structure in java because it gives almost constant time performance of O(1) for put and get operations irrespective of how big is the data. As you already know, HashMap stores the data in the form of key-value pairs. In this section, we will see how HashMap works internally in java and how it stores the elements to give O(1) performance for put and get operations.

## Internal Structure

HashMap stores the data in the form of key-value pairs. Each key-value pair is stored in an object of Entry<K, V> class. Entry<K, V> class is the static inner class of HashMap which is defined like below.

```java
static class Entry<K,V> implements Map.Entry<K,V> {
    final K key;
    V value;
    Entry<K,V> next;
    int hash;
    //Some methods are defined here
}
```

As you can see, this inner class has four fields: key, value, next and hash.

- **key** : It stores the key of an element and it is final.
- **value** : It holds the value of an element.
- **next** : It holds the pointer to next key-value pair. **This attribute makes the key-value pairs stored as a linked list.**
- **hash** : It holds the hashcode of the key.

These Entry objects are stored in an array called **table[]**. This array is initially of size 16. It is defined like below:

```java
/**
 * The table, resized as necessary. Length MUST Always be a power of two.
 */
transient Entry<K,V>[] table;
```

The picture below best summarizes the whole HashMap structure:

![https://github.com/ZhongyangMA/images/raw/master/maps-in-java/hashmap.png](https://github.com/ZhongyangMA/images/raw/master/maps-in-java/hashmap.png)

Internally HashMap uses an array of Entry<K, V> class called table\[\] to store the key-value pairs. 

But how HashMap allocates slot in table\[\] array to each of its key-value pair is very interesting. It doesn’t inserts the objects as you put them into an array i.e. first element at index 0, second element at index 1 and so on. Instead it uses the **hash code** of the key to decide the index for a particular key-value pair. It is called **Hashing**.

## What Is Hashing?

The whole HashMap data structure is based on the principle of **Hashing**. Hashing is nothing but the function or algorithm or method which when applied on any object/variable returns an unique integer value representing that object/variable. This unique integer value is called **hash code**. Hash function or simply hash said to be the best if it returns the same hash code each time it is called on the same object. Two objects can have same hash code.

*HashMap* has its own hash function to calculate the hash code of the key. Whenever you insert new key-value pair using *put()* method, *HashMap* blindly doesn’t allocate slot in the *table[]* array. Instead it calls **hash()** function on the key. 

After calculating the hash code of the key, it calls **indexFor()** method by passing the hash code of the key and length of the *table[]* array. This method returns the index in the *table[]* array for that particular key-value pair.

## How *put()* method works?

Xxxx

## How *get()* method works?

Xxxx xxx

# HashMap vs. Hashtable

Xxxx

# ConcurrentHashMap

Xxxx

# TreeMap

Xxxx

# LinkedHashMap

Xxxx

Xxxx

Xxxx

# References

[1] xxxxx

[2] xxxxx

[3] xxxxx








