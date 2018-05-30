---
layout: post
title: "Auto-Widening, Auto-Boxing and Auto-UpCasting"
date: 2016-06-10 23:32:43
categories: Java
permalink: /archivers/Auto-Widening-Auto-Boxing-Auto-UpCasting
---

_(0000 words, 0 minutes)_

This is an quite interesting topic in Java. Before I wrote this reading note I can not exactly distinguish them one from another. So let's see what auto-widening, auto-boxing and auto-upcasting mean, and under what circumstances they will happen.

<!--more-->

# The Size Of Primitive Types

The table below shows the sizes of each primitive types in Java:

| primitive types | byte | short | int  | long | float | double |
| :-------------: | :--: | :---: | :--: | :--: | :---: | :----: |
|   size (byte)   |  1   |   2   |  4   |  8   |   4   |   8    |

Please note that although the size (in byte) of **float** is less than that of **long**, the number range of **float** is still larger than **long** due to their different data structures. Thus from long to float, the auto-widening is possible.

# Some Code Examples

xxxxxx



# Conclusion

xxxxx

![https://github.com/ZhongyangMA/images/raw/master/auto-widening/auto.png](https://github.com/ZhongyangMA/images/raw/master/auto-widening/auto.png)

xxxxx



# References

[1] Auto-Widening Vs Auto-Boxing Vs Auto-UpCasting: [http://javaconceptoftheday.com/auto-widening-auto-boxing-auto-upcasting-java](http://javaconceptoftheday.com/auto-widening-auto-boxing-auto-upcasting-java)

[2] How does upcasting works in Java: [https://stackoverflow.com/questions/45456524/how-does-upcasting-works-in-java](https://stackoverflow.com/questions/45456524/how-does-upcasting-works-in-java)

[3] Wrapper class in Java: [http://javainsimpleway.com/wrapper-class-in-java](http://javainsimpleway.com/wrapper-class-in-java)

[4] Autoboxing and Unboxing: [https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)

[5] 为什么long类型的比float类型的范围小: [https://blog.csdn.net/shanshan1yi/article/details/48477119](https://blog.csdn.net/shanshan1yi/article/details/48477119)




