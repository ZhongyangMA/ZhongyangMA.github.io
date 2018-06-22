---
layout: post
title: "Some Points To Remember About Thread In Java"
date: 2016-11-09 10:20:41
categories: Java
permalink: /archivers/Some-Points-To-Remember-About-Thread-In-Java
---

Thread is a smallest executable unit of a process. Thread has it’s own path of execution in a process. Multitasking is an operation in which multiple tasks are performed simultaneously. Multitasking is used to utilize CPU’s idle time. In thread-based multitasking or multithreading, multiple threads in a process are executed simultaneously. In this reading note, I just record down some basic points to remember about thread in Java.

<!--more-->

# Creating Threads

## By extending java.lang.Thread class

```java
class MyThread extends Thread {
    @Override
    public void run() {
        //Keep the task to be performed here
    }
}
//...
MyThread myThread = new MyThread();
myThread.start();
```

## By implementing java.lang.Runnable interface

```java
class MyTask implements Runnable {
    @Override
    public void run() {
        //Keep the task to be performed here
    }
}
//...
MyTask myTask = new MyTask();
Thread t = new Thread(myTask);  // separate actual task from the runner
t.start();
```

# Thread.sleep() Method

Thread.sleep() method makes the **currently executing thread** to pause it’s execution for a specified period of time. When the thread is going for sleep, it doesn't release the synchronized locks it holds.

Thread.sleep() method throws InterruptedException if a thread in sleep is interrupted by other threads. InterruptedException is a checked type of exception. That means, “Thread.sleep()” statement must be enclosed within try-catch blocks.

```java
try {
    Thread.sleep(5000);
} catch (InterruptedException e) {
    e.printStackTrace();
}
```

It is always current thread that is going to sleep. For example, In the below example, main thread is going to sleep not “MyThread” even though you are calling sleep() method on “MyThread”.

```java
MyThread thread = new MyThread(); 
thread.start(); 
try {
    thread.sleep(5000);       //main thread is going for sleep not MyThread
} catch (InterruptedException e) {
    e.printStackTrace();
}
```

It is a bad practice to call sleep() method with an instance of Thread class as in the above example. If you want a particular thread to sleep for a while, then call sleep() method inside the run() method of that thread.

# Joining The Threads

join() method of java.lang.Thread class is used to mantain the order of execution of threads. Using join() method, you can make the currently executing thread to wait for the some other threads to finish their task. For example, Let’s us assume that there are two threads namely, thread1 and thread2. You can make thread1 to hold it’s execution for some time so that thread2 can finish it’s task. After, thread2 finishes it’s task, thread1 resumes it’s execution.For this to happen, you should call join() method on thread2 within thread1.

```java
Thread t1 = new Thread() {
    @Override
    public void run() {
        try {
            t2.join(5000);   // t1 waits at most 5 seconds for thread t2 to finish it's task
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
};
```

# Deadlock

Deadlock in Java is a condition which occurs when two or more threads get blocked waiting for each other for an infinite period of time to release the resources(Locks) they hold. Deadlock is the common problem in multi-threaded programming which can completely stops the execution of an application. So, extra care need to be taken while writing the multi-threaded programs so that deadlock never occurs.

Here is an example shows how the deadlock occurs:

```java
public class LeftRightDeadLock {
    private final Object left = new Object();
    private final Object right = new Object();
    
    public void leftRight() {
        synchronized (left) {
            synchronized (right) {
                // do something
            }
        }
    }
    
    public void rightLeft() {
        synchronized (right) {
            synchronized (left) {
                // do something
            }
        }
    }
}
```

Cyclic locking dependency causes the deadlock.

Deadlock is a dangerous condition, if it happens, it will bring the whole application to complete halt. So, extra care need to be taken to avoid the deadlock. Followings are some tips that can be used to avoid the deadlock.

- Try to **avoid nested synchronized blocks**. Nested synchronized blocks makes a thread to acquire another lock while it is already holding one lock. This may create the deadlock if another thread wants the same lock which is currently held by this thread.

- If you needed nested synchronized blocks at any cost, then make sure that threads **acquire the needed locks in some predefined order**. For example, If there are three threads t1, t2 and t3 running concurrently and they needed locks A, B and C in the following manner:

  > Thread t1: Lock A, Lock B  
  > Thread t2: Lock A, Lock C  
  > Thread t3: Lock A, Lock B, Lock C  

  If you define such lock ordering, then thread t2 never acquire lock C and t3 never acquire lock B and lock C until they got lock A. They will wait for lock A until it is released by t1. After lock A is released by t1, any one of these threads will acquire lock A on the priority basis and finishes their task. Other thread which is waiting for lock A, will never try to acquire remaining locks. By defining such lock ordering, you can avoid the deadlock.

- Another deadlock preventive tip is to **specify the time for a thread to acquire the lock**. If it fails to acquire the specified lock in the given time, then it should give up trying for a lock and retry after some time. Such method of specifying time to acquire the lock is called lock timeout. (**ReentrantLock -> tryLock()** )

# Interthread Communication

Threads can communicate with each other using **wait()**, **notify()** and **notifyAll()** methods. These methods are final methods of java.lang.Object class. That means every class in Java will have these methods.

**Important Note:** Any thread which calls these methods must have lock of that object. Thus these three methods must be called **within synchronized method or block**.

Below is an example for using wait() and notify() methods:

```java
class Shared {
    synchronized void method1() {
        try {
            wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    synchronized void method2() {
        try {
            Thread.sleep(5000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        notify();  // wakes up one thread randomly which is waiting for lock of this object
    }
}
//...
public class ThreadsInJava {
    public static void main(String[] args) {
        final Shared s = new Shared();
        Thread t1 = new Thread() {
            @Override
            public void run() {
                s.method1();
            }
        };
        Thread t2 = new Thread() {
            @Override
            public void run() {
                s.method2();
            }
        };
        t1.start();
        t2.start();
    }
}
```

In this example, Thread t1 and t2 are sharing the same object `s`, then: 

1. If t1 acquires the object lock first and enters method1(), Thread t2 has to wait for thread t1 to release the object lock. 
2. Thread t1 calls wait() method within method1(). As soon as it calls wait() method, It releases the lock of `s` object and goes for wait. 
3. Thread t2 acquires this lock and enters method2(). After entering method2(), thread t2 sleeps for 5 seconds and calls notify() method on this object. It wakes up thread t1 which is waiting for this object lock. 
4. As soon as thread t2 releases the object lock after finishing it’s execution of method2(), thread t1 acquires this lock and executes remaining statements of method1(). After t2 calls notify() and before t2 actually releases the lock, t1 is in the BLOCKED status.

In this manner, both threads t1 and t2 communicate with each other and share the lock. When a thread calls **notifyAll()** method on an object, it notifies all the threads which are waiting for this object lock. But, only one thread will acquire this object lock depending upon priority.

# Thread Interruption

Thread interruption in Java is a mechanism in which a thread which is either sleeping or waiting can be made to stop sleeping or waiting. Thread interruption is programmatically implemented using the non-static public *interrupt()* method of java.lang.Thread class.

The whole thread interruption mechanism depends on an internal flag called **interrupt status**. The initial value of this flag for any thread is false. When you call interrupt() method on a thread, interrupt status of that thread will be set to true. InterruptedException is thrown when a thread is interrupted while it is sleeping or waiting. When a thread throws InterruptedException, this status will be set to false again. Many methods of Thread class like sleep(), wait(), join() throw InterruptedException.

Here is an example for interrupting a sleeping thread using interrupt() method:

```java
public class ThreadInJava {
    public static void main(String[] args) {
        Thread t = new Thread() {
            @Override
            public void run() {
                try {
                    Thread.sleep(10000);  // Thread is sleeping for 10 seconds
                } catch (InterruptedException e) {
                    System.out.println("Thread is interrupted");
                }
            }
        };
        t.start();
        try {
            Thread.sleep(3000);  // Main thread is sleeping for 3 seconds
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        t.interrupt();  // main thread is interrupting thread t
    }
}
```

In the above example, main thread is creating and starting thread t. Thread t is going to sleep for 10 seconds as soon as it started. main thread, after starting thread t, is also going to sleep for 3 seconds. After sleeping for 3 seconds, main thread calls interrupt() method on thread t. It interrupts the sleeping of thread t. It causes the InterruptedException.

Interrupted thread will not be eligible to go for sleep. i.e. If you call interrupt() method on a thread which is not yet slept but running, such thread will not throw InterruptedException as soon as it is interrupted. Instead it will raise InterruptedException while going to sleep.

# Thread Life Cycle

xxxxx










# References

[1] xxxxxxx: []()

[2] xxxxxxx: []()

[3] xxxxxxx: []()









