---
layout: post
title: "Strings In Java"
date: 2016-05-08 09:12:03
categories: Java
permalink: /archivers/Strings-In-Java
---

_(0000 words, 00 minutes)_

Strings, which are widely used in Java programming, are a sequence of characters. "Java", “aaa”, “123” and “A” are some examples of strings, which are all enclosed within the double quotes. In the Java programming language, strings are objects. The Java platform provides three classes: **String**, **StringBuffer** and **StringBuilder** to create and manipulate strings. In this reading note, I will talk about some important aspects of Java strings and the problems we often encounter. 

<!--more-->

# Introduction To String

The Java platform provides three classes: **String**, **StringBuffer** and **StringBuilder** to create and manipulate strings. All these three classes are members of **java.lang** package and they are **final** classes. That means you can’t create subclasses to these three classes.

**java.lang.String** objects are immutable in java. That means whenever you try to modify the existing String object, a new String object is created with modifications, the existing object is not at all altered. Where as **java.lang.StringBuffer** and **java.lang.StringBuilder** objects are mutable, which means you can perform modifications to the existing objects.

**StringBuffer** and **StringBuilder** objects are mutable, and only **StringBuffer** objects are thread safe, where as **StringBuilder** objects are not. So whenever you want immutable and thread safe string objects, use **String** class and whenever you want mutable as well as thread safe string objects then use **StringBuffer** class.

In all three classes, **toString()** method is overrided. So. whenever you use reference variables of these three types, they will return contents of the objects not physical address of the objects. **equals()** and **hashCode()** methods are overrided only in **String** class but not in **StringBuffer** and **StringBuilder** classes.

In case of String class, you can create the objects without **new** operator. But in case of StringBuffer and StringBuilder class, you have to use **new** operator to create the objects.

Here is a table showing the differences between these three classes:

|                 classes                  |    java.lang.String    | java.lang.StringBuffer | java.lang.StringBuilder |
| :--------------------------------------: | :--------------------: | :--------------------: | :---------------------: |
|                  final                   |          yes           |          yes           |           yes           |
|                immutable                 |          yes           |           no           |           no            |
|               thread safe                |          yes           |          yes           |           no            |
|       **toString()** is overridden       |          yes           |          yes           |           yes           |
| **equal()** & **hashCode()** is overridden |          yes           |           no           |           no            |
|               constructor                | **new** & **literals** |        **new**         |         **new**         |

# Memory Usage Of String

We all know that JVM divides the allocated memory to a Java program into two parts. one is **Stack** and another one is **heap**. Stack is used for execution purpose and heap is used for storage purpose. In the heap memory, JVM allocates some memory specially meant for string literals. This part of the heap memory is called **String Constant Pool (SCP)**.

Whenever you create a string object using string literal, that object is stored in the **SCP**: 

```java
String s1 = "abc";
String s2 = "123";
```

One more interesting thing about **SCP** is that, pool space is allocated to an object depending upon it’s content. There will be no two objects having the same content in **SCP**.

And whenever you create a string object using **new** keyword, such object is stored in the heap memory:

```java
String s3 = new String("Java");
```

When you create a string object using string literal, JVM first checks the content of to be created object. If there exist an object in the pool with the same content, then it returns the reference of that object. It doesn’t create new object. If the content is different from the existing objects then only it creates new object.

This can be proved by using “==” operator. As “==” operator returns true if two objects have same physical address in the memory otherwise it will return false. In the below example, s1 and s2 are created using string literal “abc”. So, s1 == s2 returns true. Where as s3 and s4 are created using new operator having the same content. But, s3 == s4 returns false.

```java
String s1 = "abc";
String s2 = "abc";
System.out.println(s1 == s2);  // return true
String s3 = new String("abc");
String s4 = new String("abc");
System.out.println(s3 == s4);  // return false
```

In simple words, there can not be two string objects with same content in the string constant pool. But, there can be two string objects with the same content in the heap memory.

The examples below shows the  immutability of String:

```java
String s1 = "abc";
String s2 = "abc";
System.out.println(s1 == s2);         //Output : true 
s1 = s1 + "123"; 
System.out.println(s1 == s2);         //Output : false
```

In this example, s1 and s2 point to the same object in SCP. `s1 = s1 + "123"` appends “123” to the object to which s1 is pointing and re-assigns reference of that object back to s1. Then, compare physical address of s1 and s2 using “==” operator. This time it will return false.

That means now both s1 and s2 are pointing to two different objects in the pool. Once we tried to change the content of the object using ‘s1’, a new object is created in the pool with “abc123” as it’s content and it’s reference is assigned to s1.

**Are string objects created using new operator also immutable? The answer is Yes**.

# StringBuffer And StringBuilder

xxx

# String Intern

xxx




# References

[1] Introduction To Strings: [http://javaconceptoftheday.com/introduction-strings](http://javaconceptoftheday.com/introduction-strings)

[2] String Memory Internals: [https://dzone.com/articles/string-memory-internals](https://dzone.com/articles/string-memory-internals)

[3] The Java Tutorials - String: [https://docs.oracle.com/javase/tutorial/java/data/strings.html](https://docs.oracle.com/javase/tutorial/java/data/strings.html)

[4] Java String And Its Methods: [https://beginnersbook.com/2013/12/java-strings](https://beginnersbook.com/2013/12/java-strings)


