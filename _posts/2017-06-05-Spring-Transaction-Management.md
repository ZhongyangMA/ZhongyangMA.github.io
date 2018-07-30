---
layout: post
title: "Spring Transaction Management"
date: 2017-06-05 10:23:02
categories: Java Spring
permalink: /archivers/Spring-Transaction-Management
---

A database transaction is a sequence of actions that are treated as a single unit of work. These actions should either complete entirely or take no effect at all. Transaction management is an important part of RDBMS-oriented enterprise application to ensure data integrity and consistency. The concept of transactions can be described with the following four key properties described as **ACID**.

<!--more-->

**Atomicity** − A transaction should be treated as a single unit of operation, which means either the entire sequence of operations is successful or unsuccessful.

**Consistency** − This represents the consistency of the referential integrity of the database, unique primary keys in tables, etc.

**Isolation** − There may be many transaction processing with the same data set at the same time. Each transaction should be isolated from others to prevent data corruption.

**Durability** − Once a transaction has completed, the results of this transaction have to be made permanent and cannot be erased from the database due to system failure.



# Title

xxxx

# References

[1] 事务的传播行为: [http://blog.sina.com.cn/s/blog_4b5bc0110100z7jr.html](http://blog.sina.com.cn/s/blog_4b5bc0110100z7jr.html)

[2] Spring @Transactional 注解的详细用法: [https://www.cnblogs.com/yepei/p/4716112.html](https://www.cnblogs.com/yepei/p/4716112.html)

[3] Spring Framework Transaction Management: [https://docs.spring.io/spring-framework/docs/4.2.x/spring-framework-reference/html/transaction.html](https://docs.spring.io/spring-framework/docs/4.2.x/spring-framework-reference/html/transaction.html)

[4] Spring - Transaction Management: [https://www.tutorialspoint.com/spring/spring_transaction_management.htm](https://www.tutorialspoint.com/spring/spring_transaction_management.htm)




