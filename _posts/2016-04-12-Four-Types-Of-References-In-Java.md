---
layout: post
title: "Four Types Of References In Java"
date: 2016-04-12 17:00:11
categories: Java
permalink: /archivers/Four-Types-Of-References-In-Java
---

_(851 words, 3 minutes)_

There are actually 4 kinds of reference types in Java: strong references, soft references, weak references and phantom references. These references are different because of the existence of a garbage collection mechanism in the JVM. The decision of reclaiming memory from the object heap depends not only on the fact that there are active references to an object, but also on the type of reference to the object. Let's try to see how each of them differ from one another.

<!--more-->

# Hierarchy of References

![](https://github.com/ZhongyangMA/images/raw/master/four-reference-types/ref.png)

As shown in this figure, there are four types of references. The SoftReference, WeakReference and PhantomReference are all subclasses of Reference, which represents the strong reference.

# Strong Reference

These type of references we use daily while writing the code. Any object in the memory which has active strong reference is not eligible for garbage collection.

![strong ref](https://github.com/ZhongyangMA/images/raw/master/four-reference-types/strong.png)

As shown in this figure, reference variable ‘a’ is a strong reference which is pointing to class A-type object. At this point of time, this object can’t be garbage collected as it has strong reference.

If you make reference ‘a’ to point to null, then, object to which ‘a’ is pointing earlier will become eligible for garbage collection. Because, it will have no active references pointing to it. This object is most likely to be garbage collected when garbage collector decides to run.

# Soft Reference

The objects which are softly referenced will not be garbage collected (even though they are eligible for garbage collection) until JVM badly needs memory. You can create a soft reference to an existing object by using  **java.lang.ref.SoftReference** class.

![](https://github.com/ZhongyangMA/images/raw/master/four-reference-types/soft.png)

In the above example, you create two strong references: ‘**a**‘ and ‘**softA**‘. ‘a’ is pointing to A-type object and ‘softA’ is pointing to SoftReference type object. This SoftReference type object is internally referring to A-type object to which ‘a’ is also pointing. When ‘a’ is made to point to null, object to which ‘a’ is pointing earlier becomes eligible for garbage collection. But, it will be garbage collected only when JVM needs memory. Because, it is softly referenced by ‘softA’ object.

# Weak Reference

JVM ignores the **weak references**. That means objects which has only week references are eligible for garbage collection. They are likely to be garbage collected when JVM runs garbage collector thread. JVM doesn’t show any regard for weak references.

Look at the below picture for more clear understanding.

![](https://github.com/ZhongyangMA/images/raw/master/four-reference-types/weak.png)

You may think that what is the use of creating weak references if they are ignored by the JVM. The purpose of the use of weak reference is that you can retrieve back the weakly referenced object if it is not yet removed from the memory. This is done using get() method of WeakReference class. It will return reference to the object if object is not yet removed from the memory.

# Phantom Reference

The objects which are being referenced by **phantom references** are eligible for garbage collection. But, before removing them from the memory, JVM puts them in a queue called **‘reference queue’** . They are put in a reference queue after calling finalize() method on them. You can’t retrieve back the objects which are being phantom referenced. That means calling get() method on phantom reference always returns null.

# Usage Scenarios

**Strong References:** are written everywhere in our daily programming.

**Soft References:** will not be garbage collected (even though they are eligible for garbage collection) until JVM badly needs memory. When putting Objects into some kinds of caches, it is suitable to create the soft references. When the JVM are badly lack of memory, it will reclaim memory from these softly referenced Objects.

**Weak References:** can be retrieved back by using get() method if it is not yet removed from the memory. JVM ignores the **weak references**. That means objects which has only week references are eligible for garbage collection. Thus it can be used to monitoring whether or not an Object is removed.

**Phantom References:** are put in a reference queue after calling finalize() method on them. Thus it can be used to tracking the activity of the GC for these Objects. Phantom reference provides the mechanism that you can do something (e.g. Post-Mortem clean) after finalize() is called for these Objects.

# References

[1] Types Of References In Java: [http://javaconceptoftheday.com/types-of-references-in-java-strong-soft-weak-and-phantom](http://javaconceptoftheday.com/types-of-references-in-java-strong-soft-weak-and-phantom)

[2] Reference Types In Java: [https://dzone.com/articles/reference-types-java-part-1](https://dzone.com/articles/reference-types-java-part-1)

[3] 基于Java软引用机制最大使用JVM堆内存: [http://www.cnblogs.com/dimmacro/p/4506714.html](http://www.cnblogs.com/dimmacro/p/4506714.html)

[4] Java对象的强、软、弱和虚引用: [http://developer.51cto.com/art/200906/130447.htm](http://developer.51cto.com/art/200906/130447.htm)

[5] Java四种引用介绍及使用场景: [https://blog.csdn.net/u014532217/article/details/79184412](https://blog.csdn.net/u014532217/article/details/79184412)



