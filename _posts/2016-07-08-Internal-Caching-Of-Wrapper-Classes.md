---
layout: post
title: "Internal Caching Of Wrapper Classes"
date: 2016-07-08 18:11:21
categories: Java
permalink: /archivers/Internal-Caching-Of-Wrapper-Classes
---

I first realized this mechanism at the time when I read an interview question about the equality checking of two Integer values. As we all know, a new instance created in java takes some memory space in heap, so creating new objects is always an expensive process. To avoid this expensive object creation process, many frameworks have provided resource pooling in different ways. In Java, wrapper classes are immutable, each wrapper class stores a list of commonly used instances of its own type in form of cache. Just like string pool, they can also have their own pools.

<!--more-->

# Integer Cache Demonstration

Xxxxxx

Xxxxxx

# References

[1] Java Wrapper Classes Internal Caching: [https://howtodoinjava.com/core-java/basics/object-initialization-best-practices-internal-caching-in-wrapper-classes](https://howtodoinjava.com/core-java/basics/object-initialization-best-practices-internal-caching-in-wrapper-classes)

[2] Java语法基础包装类的缓存: [https://blog.csdn.net/u012552052/article/details/45370537](https://blog.csdn.net/u012552052/article/details/45370537)

[3] xx




