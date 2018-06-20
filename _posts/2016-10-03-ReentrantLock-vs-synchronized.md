---
layout: post
title: "ReentrantLock vs. synchronized"
date: 2016-10-03 11:51:13
categories: Java
permalink: /archivers/ReentrantLock-vs-synchronized
---

Lock provides a tool to control access to a shared resource in a multi-threaded environment. A lock provides access to only one thread at a time to the shared resource. Before Java 5.0, the only mechanisms for coordinating access to shared data were *synchronized* and *volatile*. Java 5.0 adds another option: *ReentrantLock*. ReentrantLock is a mutual exclusion lock similar to implicit lock provided by synchronized methods and statements but with additional flexibility. It is not a replacement for intrinsic locking, but rather an alternative with advanced features.

<!--more-->

# Keyword _synchronized_

## Introduction

Synchronization in Java is a strategy or a method to avoid thread interference and hence protecting the data from inconsistency. When a method or block is declared as **synchronized**, only one thread can enter into that method or block. When one thread is executing synchronized method or block, the other threads which wants to execute that method or block must wait or suspend their execution until first thread is done with that method or block. Thus avoiding the thread interference and achieving thread safeness.

```java
class Shared {
    public synchronized void NonStaticMethod(){
        // object level lock
    }
    public static synchronized void staticMethod(){
        // class level lock
    }
}
```

Sometimes, you need only some part of the method to be synchronized not the whole method. This can be achieved with synchronized blocks. Synchronized blocks must be defined inside a definition blocks like methods, constructors, static initializer or instance initializer.

Synchronized block takes one argument and it is called **mutex** (互斥体). If synchronized block is defined inside non-static definition blocks like non-static methods, instance initializer or constructors, then this mutex must be an instance of that class. If synchronized block is defined inside static definition blocks like static methods or static initializer, then this mutex must be like ClassName.class.

Here is an example of static and non-static synchronized blocks:

```java
class Shared {
    static void staticMethod() {
        synchronized (Shared.class) {
            //static synchronized block
        }
    }
    void NonStaticMethod() {
        synchronized (this) {
            //Non-static synchronized block
        }
    }
    void anotherNonStaticMethod() {
        synchronized (new Shared()) {
            //Non-static synchronized block
        }
    }
}
```

Here are some important points:

- **synchronized** keyword can not be applied to variables, constructors, static initializer and instance initializers.

  ```java
  class Shared {
      synchronized int i;    //compile time error, can't use it with variables
      synchronized public Shared() {
          //compile time error, constructors can not be synchronized
      }
      synchronized static {
          //Compile time error, Static initializer can not be synchronized
      } 
      synchronized {
          //Compile time error, Instance initializer can not be synchronized
      }
  }
  ```


- Constructors, Static initializer and instance initializer can’t be declared with synchronized keyword, but they can contain synchronized blocks.

  ```java
  class Shared {
      public Shared() {
          synchronized (this) {
              //synchronized block inside a constructor
          }
      }
      static {
          synchronized (Shared.class) {
              //synchronized block inside a static initializer
          }
      }
      {
          synchronized (this) {
              //synchronized block inside a instance initializer
          }
      }
  }
  ```


- It is possible that both static synchronized and non-static synchronized methods can run simultaneously. Because, static methods need **class level lock** and non-static methods need **object level lock**.

- A method can contain any number of synchronized blocks. This is like synchronizing multiple parts of a method.

- Synchronization blocks can be nested.

  ```java
  synchronized (this) {
      synchronized (this) {
          //Nested synchronized blocks
      }
  }
  ```


- Lock acquired by the thread before executing a synchronized method or block must be released after the completion of execution, no matter whether execution is completed normally or abnormally (due to exceptions).
- Synchronization in java is **Re-entrant in nature**. A thread can not acquire a lock that is owned by another thread. But, a thread can acquire a lock that it already owns. That means if a synchronized method gives a call to another synchronized method which needs same lock, then currently executing thread can directly enter into that method or block without acquiring the lock.
- synchronized method or block is very slow. They decrease the performance of an application. So, special care need to be taken while using synchronization. Use synchronization only when you needed it the most. Use synchronized blocks instead of synchronized methods. Because, synchronizing some part of a method improves the performance than synchronizing the whole method.

## Principle

The synchronization in Java is built around an entity called **lock** or **monitor**. Here is the brief description about lock or monitor:

- Whenever an object is created to any class, an object lock is created and is stored inside the object.
- One object will have only one object lock associated with it.
- Any thread wants to enter into synchronized methods or blocks of any object, they must acquire object lock associated with that object and release the lock after they are done with the execution.
- The other threads which wants to enter into synchronized methods of that object have to wait until the currently executing thread releases the object lock.
- To enter into static synchronized methods or blocks, threads have to acquire **class lock** associated with that class as static members are stored inside the class memory.

# ReentrantLock

 Using synchronized methods and statements provides access to an implicit lock for an object. However in certain scenarios we might need access explicitly to the lock object for flexibility. 

## Interface and Methods

Xxxxxx

## Examples

Xxxxxx

# Reentrancy

Xxxxxx

# Fairness

Xxxxxx

# ReentrantLock vs. synchronized

## Differences

Xxxxxx

## Performance

Xxxxxx

# Read-write Locks

Xxxxxx

# References

[1] Java ReentrantLock: [http://www.sourcetricks.com/2014/09/java-reentrantlock.html](http://www.sourcetricks.com/2014/09/java-reentrantlock.html)

[2] Java中的ReentrantLock和synchronized两种锁定机制的对比: [https://www.ibm.com/developerworks/cn/java/j-jtp10264/index.html](https://www.ibm.com/developerworks/cn/java/j-jtp10264/index.html)

[3] 关于synchronized和ReentrantLock之多线程同步详解: [https://www.jianshu.com/p/96c89e6e7e90](https://www.jianshu.com/p/96c89e6e7e90)

[4] Synchronization in Java: [http://javaconceptoftheday.com/synchronization-in-java](http://javaconceptoftheday.com/synchronization-in-java)

[5] xxxxxxx: []()

[6] xxxxxxx: []()








