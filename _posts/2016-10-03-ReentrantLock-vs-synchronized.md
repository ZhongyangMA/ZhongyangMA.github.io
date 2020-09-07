---
layout: post
title: "ReentrantLock vs. synchronized"
date: 2016-10-03 11:51:13
categories: Java
permalink: /archivers/ReentrantLock-vs-synchronized
highlight: true
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
- One object will have only one **object lock** associated with it.
- Any thread wants to enter into synchronized methods or blocks of any object, they must acquire object lock associated with that object and release the lock after they are done with the execution.
- The other threads which wants to enter into synchronized methods of that object have to wait until the currently executing thread releases the object lock.
- To enter into static synchronized methods or blocks, threads have to acquire **class lock** associated with that class as static members are stored inside the class memory.

# ReentrantLock

Using synchronized methods and statements provides access to an implicit lock for an object. However in certain scenarios we might need access explicitly to the lock object for flexibility.

## Interface and Methods

Java supports **Lock** interface in the package *java.util.concurrent.locks*. **ReentrantLock** implements the Lock interface and provides the re-entrant mutual exclusive lock. Some of the key APIs of ReentrantLock are listed below:

- *ReentrantLock()* - Creates ReentrantLock instance.
- *void lock()* - Acquires the lock.
- *void unlock()* - Releases the lock.
- *boolean tryLock()* - Acquires the lock only if the lock is not held by another thread.
- *boolean isLocked()* - Checks if lock is held by another thread.

## Examples

The code below shows the canonical form for using a Lock. The Lock must be released in a `finally` block. Otherwise the lock would never be released if the guarded code were to throw an exception. Failing to use `finally` to release a Lock is a ticking time bomb. 

```java
Lock lock = new ReentrantLock();
lock.lock();  // thread will be blocked here until it gets the lock.
try {
    // do something
} finally {
    lock.unlock();
}
```

The timed and polled lock-acquisition modes provided by `TryLock` allow more sophisticated error recovery than unconditional acquisition. With intrinsic locks, a deadlock is fatal - the only way to recover is to restart your application. Timed and polled locking offer another option: probabilistic deadlock avoidance. The timed lock acquisition allows exclusive locking to be used within time-limited activities. In the example below, by using tryLock() method to make sure the thread waits only for definite time and if it’s not getting the lock on object, it’s just exiting.

```java
Lock lock = new ReentrantLock();
try {
    if(lock.tryLock(10, TimeUnit.SECONDS)) {  // return (true/false) immediately or within a limited time
        // do something
    }
} catch (InterruptedException e) {
    e.printStackTrace();
} finally {
    lock.unlock();
}
```

Just as timed lock acquisition allows exclusive locking to be used within time-limited activities, interruptible lock acquisition allows locking to be used within cancellable activities. The `lockInterruptibly` method allows a thread to try to acquire a lock while remaining responsive to interruption.

```java
lock.lockInterruptibly();
try {
    // do something
} finally {
    lock.unlock();
}
```

Invoking `thread.interrupt()` will make this thread immediately give up waiting for the lock, and release all the locks it has already acquired.

# Fairness

Threads acquire a fair lock in the order in which they request it, whereas a nonfair lock permits barging: threads requesting a lock can jump ahead of the queue of waiting threads if the lock happens to be available when it is requested.

In principle, there are different policies possible, but the most efficient is to make the lock "unfair" and allow "barging". The intrinsic lock is "unfair", and the "unfair" behavior is also the default behavior of ReentrantLock.

When instantiating ReentrantLock, it is possible to specify its policy as "fair". In other words, when the lock is released, waiters will acquire it in strict first-come-first-served order.

```java
Lock lock = new ReentrantLock();  // default: unfair lock
Lock lock = new ReentrantLock(false);  // specify unfair
Lock lock = new ReentrantLock(true);  // specify fair
```

In some real-time applications it may be worth paying the throughput penalty to try and ensure some kind of maximum delay on lock acquisition by any one thread. But **usually lock fairness is unnecessary and the penalty is not worth paying**.

# ReentrantLock vs. synchronized

ReentrantLock provides the same locking and memory semantics as intrinsic locking, as well as additional features such as timed lock waits, interruptible lock waits, fairness. The performance of ReentrantLock appears to dominate that of intrinsic locking, winning slightly on Java 6 and dramatically on Java 5. So why not deprecate *synchronized* and encourage all new concurrent code to use *ReentrantLock*? Some authors have in fact  suggested this, treating *synchronized* as a "legacy" construct. But this is taking a good thing way too far.

Intrinsic locks still have significant advantages over explicit locks. The notation is familiar and compact, and many existing programs already use intrinsic locking. ReentrantLock is definitely a more dangerous tool than synchronization: if you forget to wrap the `unlock()` call in a finally block, your code will probably appear to run properly, but you've created a time bomb that may well hurt innocent bystanders. Save ReentrantLock for situations in which you need something ReentrantLock provides that intrinsic locking doesn't.

ReentrantLock is an advanced tool for situations where intrinsic locking is not practical. Use it if you need its advanced features: timed, polled, or interruptible lock acquisition, fair queuing, or non-block-structured locking. Otherwise, prefer *synchronized*.

# Read-write Locks

Mutual exclusion is a conservative locking strategy that prevents writer/writer and writer/reader overlap, but also prevents reader/reader overlap. In many cases, data structures are "read-mostly" they are mutable and are sometimes modified, but most accesses involve only reading. In these cases, it would be nice to relax the locking requirements to allow multiple readers to access the data structure at once. As long as each thread is guaranteed an up-to-date view of the data and no other thread modifies the data while the readers are viewing it, there will be no problems. This is what read-write locks allow: a resource can be accessed by multiple readers or a single writer at a time, but not both.

The code below shows how to wrap a Map with a read-write lock:

```java
public class ReadWriteMap<K, V> {
    private final Map<K, V> map;
    private final ReadWriteLock lock = new ReentrantReadWriteLock();
    private final Lock r = lock.readLock();
    private final Lock w = lock.writeLock();
  
    public ReadWriteMap(Map<K, V> map) {
        this.map = map;
    }
  
    public V put(K key, V value) {
        w.lock();
        try {
            return map.put(key, value);
        } finally {
            w.unlock();
        }
    }
  
    public V get(K key) {
        r.lock();
        try {
            return map.get(key);
        } finally {
            r.unlock();
        }
    }
}
```

# References

[1] Java ReentrantLock: [http://www.sourcetricks.com/2014/09/java-reentrantlock.html](http://www.sourcetricks.com/2014/09/java-reentrantlock.html)

[2] Java中的ReentrantLock和synchronized两种锁定机制的对比: [https://www.ibm.com/developerworks/cn/java/j-jtp10264/index.html](https://www.ibm.com/developerworks/cn/java/j-jtp10264/index.html)

[3] 关于synchronized和ReentrantLock之多线程同步详解: [https://www.jianshu.com/p/96c89e6e7e90](https://www.jianshu.com/p/96c89e6e7e90)

[4] Synchronization in Java: [http://javaconceptoftheday.com/synchronization-in-java](http://javaconceptoftheday.com/synchronization-in-java)

[5] Java Concurrency In Practice: Brian Goetz, Tim Peierls, Joshua Bloch, Joseph Bowbeer, David Holmes, Doug Lea. 











