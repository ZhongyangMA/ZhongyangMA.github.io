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

xxxx







# References

[1] xxxxxxx: []()

[2] xxxxxxx: []()

[3] xxxxxxx: []()







