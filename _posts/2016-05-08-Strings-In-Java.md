---
layout: post
title: "Strings In Java"
date: 2016-05-08 09:12:03
categories: Java
permalink: /archivers/Strings-In-Java
---

_(2154 words, 8 minutes)_

Strings, which are widely used in Java programming, are a sequence of characters. "Java", “aaa”, “123” and “A” are some examples of strings, which are all enclosed within the double quotes. In the Java programming language, strings are objects. The Java platform provides three classes: **String**, **StringBuffer** and **StringBuilder** to create and manipulate strings. In this reading note, I will talk about some important aspects of Java strings and the problems we often encounter. 

<!--more-->

# Introduction To String

The Java platform provides three classes: **String**, **StringBuffer** and **StringBuilder** to create and manipulate strings. All these three classes are members of **java.lang** package and they are all **final** classes. That means you can’t create subclasses to these three classes.

**java.lang.String** objects are immutable in java. That means whenever you try to modify the existing String object, a new String object is created with modifications, the existing object is not at all altered. Where as **java.lang.StringBuffer** and **java.lang.StringBuilder** objects are mutable, which means you can perform modifications to the existing objects.

**StringBuffer** and **StringBuilder** objects are mutable, and only **StringBuffer** objects are thread safe, where as **StringBuilder** objects are not. So whenever you want immutable and thread safe string objects, use **String** class and whenever you want mutable as well as thread safe string objects then use **StringBuffer** class.

In all three classes, **toString()** method is overrided. So. whenever you use reference variables of these three types, they will return contents of the objects not physical address of the objects. **equals()** and **hashCode()** methods are overrided only in **String** class but not in **StringBuffer** and **StringBuilder** classes.

In case of **String** class, you can create the objects without **new** operator. But in case of **StringBuffer** and **StringBuilder** class, you have to use **new** operator to create the objects.

Here is a table showing the differences between these three classes:

|                   classes                   |    java.lang.String    | java.lang.StringBuffer | java.lang.StringBuilder |
| :-----------------------------------------: | :--------------------: | :--------------------: | :---------------------: |
|                    final                    |          yes           |          yes           |           yes           |
|                  immutable                  |          yes           |           no           |           no            |
|                 thread safe                 |          yes           |          yes           |           no            |
|        **toString()** is overridden         |          yes           |          yes           |           yes           |
| **equal()** & **hashCode()** are overridden |          yes           |           no           |           no            |
|                 constructor                 | **new** & **literals** |        **new**         |         **new**         |

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

When you create a string object using string literal, JVM first checks the content of to be created object. If there exists an object in the pool with the same content, then it returns the reference of that object. It doesn’t create new object. If the content is different from the existing objects then it will create a new object.

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

# The Immutability of String

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

Are string objects created using **new** operator also immutable? The answer is Yes. String objects created using new operator are also immutable although they are stored in the heap memory. This can be also proved with help of an example:

```java
String s1 = new String("abc");
System.out.println(s1);         //Output : abc
s1.concat("123");
System.out.println(s1);         //Output : abc
```

Once I tried to concatenate “123” to an existing string “abc”, a new string object is created with “abc123” as it’s content. But we don’t have the reference to that object in this program. The object which s1 is pointing to hadn't been changed at all.

Immutability is the fundamental property of string objects. In whatever way you create the string objects, either using string literals or using new operator, they are immutable.

# Equality Checking

**“==” operator** compares the two objects on their physical address. That means if two references are pointing to same object in the memory, then comparing those two references using “==” operator will return true.

**equals() method**, if not overridden, will perform same comparison as “==” operator does i.e comparing the objects on their physical address. So, it is always recommended that you should override **equals()** method in your class so that it provides field by field comparison of two objects. This type of comparison is called **“Deep Comparison”**.

In java.lang.String class, equals() method is overridden to provide the comparison of two string objects based on their contents.

**hashCode() method** returns hash code value of an object in the Integer form. It is recommended that whenever you override equals() method, you should also override hashCode() method so that **two equal objects according to equals() method must return same hash code values**. This is the general contract between equals() and hashCode() methods that must be maintained all the time.

In java.lang.String class, hashCode() method is also overrided so that two equal string objects according to equals() method will return same hash code values. That means, if s1 and s2 are two equal string objects according to equals() method, then invoking **s1.hashCode() == s2.hashCode()** will return true. 

**But two unequal string objects according to equals() method may have same hash code values**. The example is:

```java
String s1 = "0-42L";
String s2 = "0-43-";
System.out.println(s1.equals(s2));  // It will also return false
System.out.println(s1.hashCode() == s2.hashCode());  // It will return true
```

It is recommended not to use hashCode() method to check the equality of two string objects. You may get unexpected result.

# StringBuffer And StringBuilder

String objects created using **String** class are immutable. Once they are created, they can not be modified. If you try to modify them, a new string object will be created with modified content. This property of String class may cause some memory issues for applications which need frequent modification of string objects. To overcome this behavior of String class, two more classes are introduced in Java to represent the strings. They are **StringBuffer** and **StringBuilder**.

You can change the contents of StringBuffer and StringBuider objects at any time of execution. When you change the content, new objects are not created. Instead of that the changes are applied to existing object. Thus solving memory issues may caused by **String** class.

You have to use **new** operator to create objects to StringBuffer and StringBuilder classes. You can’t use string literals to create objects to these classes. For example, you can’t write **StringBuffer sb = “JAVA”** or **StringBuilder sb = “JAVA”**. It gives compile time error. But, you can use both string literals and new operator to create objects to String class.

As objects of StringBuffer and StringBuilder are created using only new operator, they are stored in **heap memory**. Where as objects of String class are created using both string literals and new operator, they are stored in string constant pool as well as heap memory.

Any immutable object in java is thread safety. Because they are unchangeable once they are created. This applies to objects of **String** class also. Of the **StringBuffer** and **StringBuilder** objects, only **StringBuffer** objects are thread safety. All necessary methods in **StringBuffer** class are **synchronized** so that only one thread can enter into it’s object at any point of time. Where as **StringBuilder** objects are not thread safety.

In StringBuffer and StringBuilder classes, **equals()** and **hashCode()** methods are not overridden. Where as in String class they are overridden. **toString()** method is overridden in all three classes. You can also convert StringBuffer and StringBuilder objects to String type by calling **toString()** method on them.

# String Intern

Just imagine creating 1000 string objects with same content in heap memory and one string object with that content in String Constant Pool. Which one saves the memory? Which one will save the time? Which one will be accessed faster? It is, of course, String Constant Pool. That’s why you need String Constant Pool.

**String intern** or simply **intern** refers to string object in the String Constant Pool. **Interning** is the process of creating a string object in String Constant Pool which is an exact copy of string object in heap memory.

**intern()** method of **String** class is used to perform **interning** i.e creating an exact copy of heap string object in string constant pool. When you call this method on a string object, first it checks whether there exists an object with the same content in the String Constant Pool. If such object doesn't exist in the pool, it will create an object with the same content in the string constant pool and returns the reference of that object. If the object exists in the pool then it returns reference of that object without creating a new object.

```java
String s1 = "JAVA";
String s2 = new String("JAVA"); 
String s3 = s2.intern();       //Creating String Intern 
System.out.println(s1 == s3);       //Output : true
```

Look at this example. Object s1 will be created in string constant pool as we are using string literal to create it and object s2 will be created in heap memory as we are using new operator to create it. When you call **intern()** method on s2, it returns reference of object to which s1 is pointing as its content is the same with s2. It does not create a new object in the pool. So, s1 == s3 will return true as both of them are pointing to the same object in the pool.

When you call intern() on the string object created using string literals, it returns the reference of itself. Because, you can’t have two string objects in the pool with the same content. That means string literals are automatically interned in java.

Using s1.intern() == s2.intern() will be more fast then s1.equals(s2). Because, equals() method performs character by character comparison where as “==” operator just compares references of objects.


# References

[1] Introduction To Strings: [http://javaconceptoftheday.com/introduction-strings](http://javaconceptoftheday.com/introduction-strings)

[2] String Memory Internals: [https://dzone.com/articles/string-memory-internals](https://dzone.com/articles/string-memory-internals)

[3] The Java Tutorials - String: [https://docs.oracle.com/javase/tutorial/java/data/strings.html](https://docs.oracle.com/javase/tutorial/java/data/strings.html)

[4] Java String And Its Methods: [https://beginnersbook.com/2013/12/java-strings](https://beginnersbook.com/2013/12/java-strings)


