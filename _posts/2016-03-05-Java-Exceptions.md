---
layout: post
title: "Java Exceptions"
date: 2016-03-05 15:43:47
categories: Java
permalink: /archivers/Java-Exceptions
highlight: true
---

An Exception is an abnormal condition which occurs during run time and disrupts the normal flow of the program. Exceptions must be handled to maintain the normal flow of the program. In this reading note, I record some key points about the concepts, the handling methods and the frequently asked interview questions of Java Exception. 

<!--more-->

# Hierarchy of Exceptions

![](https://github.com/ZhongyangMA/images/raw/master/java-exceptions/hierarchy.png)

## java.lang.Throwable

java.lang.Throwable is the super class of all errors and exceptions in java. Throwable class extends java.lang.Object class. The only argument of catch block must be it’s type or it’s sub class type. It has two sub classes:

- java.lang.Error
- java.lang.Exception

## java.lang.Error

All sub classes of Error class are unchecked type of exceptions. i.e They occur during run time only.

## java.lang.Exception

java.lang.Exception is the super class for all types of Exceptions in java. All sub classes of RunTimeException are unchecked type of exceptions, i.e. They occur during run time only. While others are checked type of exceptions.

## Checked And Unchecked Exceptions

### Checked exceptions

**Checked exceptions** are the exceptions which are known during compile time. These are the exceptions that are checked at compile time. They are also called **compile time exceptions**.

These exceptions **must** be handled either using try-catch blocks or using throws clause. If not handled properly, they will give **compile time error**.

All sub classes of java.lang.Exception except sub classes of RunTimeException are checked exceptions.

### Unchecked exceptions

**Unchecked exceptions** are the exceptions which are known at run time. They can not be known at compile time because they occur only at run time. That’s why they are also called **Run Time Exceptions**. All the sub classes of **RunTimeException** and all sub classes of **Error** class are unchecked.

If any statement in the program throws unchecked exceptions and you are not handling them either using try-catch blocks or throws clause, then **it does not give compile time error**. Compilation will be successful but program may fail at run time.

# Try, Catch and Finally Blocks

Try, catch and finally keywords are main fundamentals of exception handling in java.

**try block :** In try block, keep those statements which may throw exceptions during run time.

**catch block :** This block handles the exceptions thrown by try block. It takes one argument of type java.lang.Exception.

**finally block :** Whether exception is thrown or not and thrown exception is caught or not, this block will be always executed.

```java
public class ExceptionHandling {
    public static void main(String[] args) {
        try {
            //...
        } catch(Exception ex) {
            //...
        } finally {
            System.out.println("This block is always executed");
        }
    }
}
```

## Multiple Catch Blocks

In some cases, A single statement may throw more than one type of exception. In such cases, Java allows you to put more than one catch blocks. One catch block handles one type of exception.  When an exception is thrown by the try block, all the catch blocks are examined in the order they appear and one catch block which matches with exception thrown will be executed.

```java
public class ExceptionHandling {
    public static void main(String[] args) {
        try {
            //...
        } catch(AaaException ex) {
            //...
        } catch(BbbException ex) {
            //...
        } catch(Exception ex) {  //should be placed at last
            //...
        }finally {
            System.out.println("This block is always executed");
        }
    }
}
```

The order of catch blocks should be from most specific to most general ones. i.e. Sub classes of Exception must come first and super classes later. If you keep the super classes first and sub classes later, you will get compile time error : **Unreachable Catch Block**.

## Nested try catch Blocks

In Java, try-catch blocks can be nested. i.e one try block can contain another try-catch block. 

```java
try {     //Outer try block
    try {    //Inner try block
    } catch(Exception ex) { //Inner catch block
    }
} catch(Exception ex) {     //Outer catch block
}
```

If the exception thrown by the inner try block can not be caught by it’s catch block, then this exception is propagated to outer try blocks. Any one of the outer catch block should handle this exception otherwise program will terminate abruptly.

## Return Value From try-catch-finally Blocks

Let's go through some examples and analyze the outputs.

```java
public String valueReturnTest() {
    String s = null;
    try {
        s = "return from try block";
        return s;
    } catch(Exception e) {
        s = "return from catch block";
        return s;
    } finally {
        s = "return from finally block";
    }
}
```

The output of this example is: "return from try block".

Catch blocks are executed only when exception is raised in try block. Finally block is always executed whether exception is raised or not.

However, there’s no return statement in finally block. The moment it returns `s` in try block, the value is “return from try block”, hence that value is returned, regardless of what happens to `s` after the return.

The value of `s` is update in try block and also in finally block in the above example, but we can see only the value returned by try block as we are not returning from finally block.

```java
public String valueReturnTest() {
    String s = null;
    try {
        s = "return from try block";
        return s;
    } catch(Exception e) {
        s = "return from catch block";
        return s;
    } finally {
        s = "return from finally block";
        return s;
    }
}
```

The output of this example is: "return from finally  block".

Finally block will be always executed even though try and catch blocks are returning the control. Finally block overrides any return values from try and catch blocks.

# Throw and Throws

## Throwing And Re-Throwing An Exception

We all know that exceptions occurred in the try block are caught in catch block. Thus caught exceptions can be re-thrown using **throw** keyword. Re-thrown exception must be handled some where in the program, otherwise program will terminate abruptly. For example:

```java
public void throwExample() {
    try {
        String s = null;
        System.out.println(s.length());   //This statement throws NullPointerException
    } catch(NullPointerException ex) {
        throw ex;     //Re-throwing NullPointerException
    }
}
```

## _throws_ Keyword

If a method is capable of throwing an exception that it could not handle, then it should specify that exception using throws keyword. It helps the callers of that method in handling that exception.

```java
return_type method_name(parameter_list) throws exception_list {
    //some statements
}
```

where, `exception_list` is the list of exceptions that method may throw, which must be separated by commas. The main use of throws keyword in java is that an exception can be propagated through method calls.

## Method Overriding With throws Clause

If super class method is not throwing any exceptions, then it can be overrided with any unchecked type of exceptions, but can not be overrided with checked type of exceptions.

If a super class method is throwing unchecked exception, then it can be overrided in the sub class with same exception or any other unchecked exceptions but can not be overrided with checked exceptions.

If super class method is throwing checked type of exception, then it can be overrided with same exception or with it’s sub class exceptions.

In conclusion,  you can decrease the scope of the exception, but can not be overrided with it’s super class exceptions.

# User Defined Exceptions

User defined exceptions must extend any one of the classes in the hierarchy of exceptions.

```java
class UserDefinedException extends Exception {
    String errorMessage;
    public UserDefinedException(String errorMessage) {
        this.errorMessage = errorMessage;
    }
    //Modify toString() to display customized error message
    @Override
    public String toString() {
        return errorMessage;
    }
}
```

User defined Exceptions could help user understand why the exception has occurred. To do this, create one sub class to Exception class and override toString() method.

# Interview Questions

## Difference Between final, finally and finalize

**final** is a keyword which is used to make a variable or a method or a class as “unchangeable“.

```java
final int i = 10;    //final variable
i = 20;      //Compile time error, Value can not be changed
```

A method declared as **final** can not be overridden or modified in the sub class.

```java
class SuperClass {
    final void methodOfSuperClass() {
        System.out.println("final Method");
    }
}
class SubClass extends SuperClass {
    void methodOfSuperClass() {
        //Compile time error, final method can not be overridden.
    }
}
```

A class declared as final can not be extended.

```java
final class SuperClass {
    //final class
}
class SubClass extends SuperClass {
    //Compile time error, can not create a sub class to final class
}
```

**finally** is a block which is used for exception handling along with try and catch blocks. finally block is always executed whether exception is raised or not and raised exception is handled or not. Most of time, this block is used to close the resources like database connection, I/O resources etc.

**finalize() method** is a protected method of **java.lang.Object** class. It is inherited to every class you create in java. This method is called by garbage collector thread before an object is removed from the memory. finalize() method is used to perform some clean up operations on an object before it is removed from the memory.

But, using finalize() method to close the resources is **less recommended** as it is not guaranteed that garbage collector will always call finalize() method on an object before it is removed from the memory. If it is not called, the resources will remain open. Therefore, it is always good to close the resources soon after their use using **finally block**.

## Difference Between throw, throws and Throwable

**throw** is a keyword in java which is used to throw an exception manually.

**throws** is a keyword in java which is used in the method signature to indicate that this method may throw mentioned exceptions.

**Throwable** is a super class for all types of errors and exceptions in java. This class is a member of **java.lang** package. Only instances of this class or it’s sub classes are thrown by the java virtual machine or by the throw statement.

## Difference Between Error vs. Exception

Both **java.lang.Error** and **java.lang.Exception** classes are sub classes of **java.lang.Throwable** class**,** but there exist some significant differences between them. 

**java.lang.Error** class represents the errors which are mainly caused by the **environment** in which application is running. For example, **OutOfMemoryError** occurs when JVM runs out of memory or **StackOverflowError** occurs when stack overflows.

Whereas **java.lang.Exception** class represents the exceptions which are mainly **caused by the application itself**. 

## Difference Between ClassNotFoundException vs. NoClassDefFoundError

**ClassNotFoundException** is an exception which occurs when you try to load a class at run time using **Class.forName()** or **loadClass()** methods and mentioned classes are not found in the classpath. It is thrown by the application itself. It is thrown by the methods like Class.forName(), loadClass() and findSystemClass(). It occurs when classpath is not updated with required JAR files.

On the other hand, **NoClassDefFoundError** is an error which occurs when a particular class is present at compile time but it was missing at run time. It is thrown by the Java Runtime System. It occurs when required class definition is missing at run time.

# References

[1] Exception Handling In Java: [http://javaconceptoftheday.com/exception-handling-java](http://javaconceptoftheday.com/exception-handling-java)

[2] 深入理解Java异常处理机制: [https://blog.csdn.net/hguisu/article/details/6155636](https://blog.csdn.net/hguisu/article/details/6155636)  

[3] Effective Java, Programming Language Guide. Joshua Bloch. ISBN: 0-201-31005-8. 2001

[4] java.lang.Exception 中常见异常的解释: [https://blog.csdn.net/kunshanyuZ/article/details/63257690](https://blog.csdn.net/kunshanyuZ/article/details/63257690)

[5] Java 里的异常(Exception)详解: [https://blog.csdn.net/nvd11/article/details/19047047](https://blog.csdn.net/nvd11/article/details/19047047)

[6] Java异常面试题及编程题: [https://blog.csdn.net/Bubble1210/article/details/50776103](https://blog.csdn.net/Bubble1210/article/details/50776103)

[7] Java异常处理面试题归纳: [https://blog.csdn.net/caohaicheng/article/details/38025329](https://blog.csdn.net/caohaicheng/article/details/38025329)

