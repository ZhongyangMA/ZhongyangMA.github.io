---
layout: post
title: "Spring AOP With AspectJ"
date: 2017-04-30 19:35:17
categories: Java Spring
permalink: /archivers/Spring-AOP-With-AspectJ
---

One of the key components of Spring Framework is the Aspect oriented programming (AOP) framework. The functions that span multiple points of an application are called cross-cutting concerns and these cross-cutting concerns are conceptually separate from the application's business logic. AspectJ is one of the most well-known implementations for AOP in Java. In this article we will concentrate on AspectJ’s implementation of AOP and how it works in Java.

<!--more-->

# Some Key Terms

Before we start working with AOP, let's become familiar with the AOP concepts and terminology. These terms are not specific to Spring, rather they are related to AOP.

**Aspect:** This is a module (a class) which has a set of APIs providing cross-cutting requirements. For example, a logging module would be called AOP aspect for logging. An application can have any number of aspects depending on the requirement.

**Join point:** This represents a point in your application where you can plug-in the AOP aspect. You can also say, it is the actual place in the application where an action will be taken using Spring AOP framework.

**Advice:** This is the actual action to be taken either before or after the method execution. This is an actual piece of code that is invoked during the program execution by Spring AOP framework.

**Pointcut:** This is a set of one or more join points where an advice should be executed. You can specify pointcuts using expressions or patterns as we will see in our AOP examples.

**Introduction:** An introduction allows you to add new methods or attributes to the existing classes.

**Target object:** The object being advised by one or more aspects. This object will always be a proxied object, also referred to as the advised object.

**Weaving:** Weaving is the process of linking aspects with other application types or objects to create an advised object. This can be done at compile time, load time, or at runtime.

# Types of Advice

**before:** Run advice before the a method execution.

**after:** Run advice after the method execution, regardless of its outcome.

**after-returning:** Run advice after the a method execution only if method completes successfully.

**after-throwing:** Run advice after the a method execution only if method exits by throwing an exception.

**around:** Run advice before and after the advised method is invoked.




# References

[1] AOP With Spring Framework: [https://www.tutorialspoint.com/spring/aop_with_spring.htm](https://www.tutorialspoint.com/spring/aop_with_spring.htm)

[2] The Basics of AOP: [https://blog.jayway.com/2015/09/07/the-basics-of-aop](https://blog.jayway.com/2015/09/07/the-basics-of-aop)


