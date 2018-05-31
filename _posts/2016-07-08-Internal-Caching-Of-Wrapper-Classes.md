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

# Modifying Cache Size

If you want to store a bigger number of instances, you can use runtime parameter as below:

```xml
-Djava.lang.Integer.IntegerCache.high=2000
```

Above statement will cause the cache to store instances from -127 to 1000. Remember, there is no such property like `-Djava.lang.Integer.IntegerCache.low` as for now. May be in future, it might be added as well.

# Other Wrapper Classes

Besides Integer class, other wrapper classes also provide this caching facility. Let's see them in short:

1. java.lang.Boolean store two inbuilt instances TRUE and FALSE, and return their reference if new keyword is not used.
2. java.lang.Character has a cache for chars between unicodes 0 and 127 (ascii-7 / us-ascii).
3. java.lang.Long has a cache for long between -128 to +127.
4. java.lang.String has a whole new concept of string pool.

The table below shows the cache value range of all wrapper classes:

| wrapper class | primitive type | value range of cached instances        |
| ------------- | -------------- | -------------------------------------- |
| Boolean       | boolean        | true, false (all value)                |
| Byte          | byte           | -128~127 (all value)                   |
| Short         | short          | -128~127                               |
| Character     | char           | 0~127                                  |
| Integer       | int            | -128~127 (upper limit can be modified) |
| Long          | long           | -128~127                               |
| Float         | float          | none                                   |
| Double        | double         | none                                   |



# References

[1] Java Wrapper Classes Internal Caching: [https://howtodoinjava.com/core-java/basics/object-initialization-best-practices-internal-caching-in-wrapper-classes](https://howtodoinjava.com/core-java/basics/object-initialization-best-practices-internal-caching-in-wrapper-classes)

[2] Java语法基础包装类的缓存: [https://blog.csdn.net/u012552052/article/details/45370537](https://blog.csdn.net/u012552052/article/details/45370537)






