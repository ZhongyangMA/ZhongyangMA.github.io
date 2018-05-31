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

Similar with **String** class, all wrapper classes are immutable, once their instances are created, they cannot be modified. The **equal()** method is also overridden in wrapper classes, thus it compares two objects based on their values rather than physical addresses.

In **Integer** class there is an inner class i.e. **IntegerCache**. When you assign a new **int** to Integer type like below:

```java
Integer i = 10;      //OR
Integer i = Integer.valueOf(10);
```

An already created Integer instance is returned and reference is stored in `i`. Please note that if you use `new Integer(10);` then a new instance of Integer class will be created and caching will not come into picture. It's only available when you use `Integer.valueOf()` OR directly primitive assignment (auto-boxing, which ultimately uses `valueOf()` function).

Lets see how this `IntegerCache` look like in code:

```java
private static class IntegerCache {
    private IntegerCache(){}
    static final Integer cache[] = new Integer[-(-128) + 127 + 1];
    static {
        for(int i = 0; i < cache.length; i++)
            cache[i] = new Integer(i - 128);
    }
}
```

And this is how `valueOf()` method looks like:

```java
public static Integer valueOf(int i) {
    final int offset = 128;
    if (i >= -128 && i <= 127) {
        return IntegerCache.cache[i + offset];
    }
    return new Integer(i);
}
```

As you see, IntegerCache is static inner class and will only be initialized when used first time. So on first time, due to cache creation, time might be little longer and after that it will not take more time. But, the actual benefit is memory reuse.

Let's see an example:

```java
public class IntegerCacheDemo { 
    public static void main(String[] args) { 
        Integer a1 = 100;
        Integer a2 = 100;
        Integer a3 = new Integer(100);
        System.out.println(a1 == a2);  // output: true
        System.out.println(a1 == a3);  // output: false
    }
}
```

The first print statement will print true, which means both variables are referring to the same instance. But the second print statement prints false, because `new Integer(100)` created a new fresh instance in separate memory location. So if you want to make use of this cache, always use primitive assignment to reference variable or use `valueOf()` method.

```java
Integer a1 = 100;
Integer a2 = 100;
System.out.println(a1 == a2);  // output: true
Integer a3 = 300;
Integer a4 = 300;
System.out.println(a1 == a2);  // output: false
a1 = null;  // will not make any object available for GC at all
a3 = null;  // will make the newly created Integer object available for GC
```

`a1 == a2` is true because they are pointing to the same Integer instance in the cache. `a3 == a4` is false, because their values go beyond the upper limit 127, so new instances are created.

Assigning `null` to a1 will not make any object available for GC at all, because the object which a1 was pointing to is an Integer cache instance; Whereas assigning `null` to a3 will make the newly created Integer object available for GC.

# Modifying Cache Size

If you want to store a bigger number of instances, you can use runtime parameter as below:

```xml
-Djava.lang.Integer.IntegerCache.high=1000
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






