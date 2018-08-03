---
layout: post
title: "Distributed Transactions"
date: 2017-07-23 19:21:46
categories: microservice architecture
permalink: /archivers/Distributed-Transactions
---

Local transactions are specific to a single transactional resource like a JDBC connection, whereas global transactions can span multiple transactional resources like transaction in a distributed system. A distributed or a global transaction is executed across multiple systems, and its execution requires coordination between the global transaction management system and all the local data managers of all the involved systems.

<!--more-->

# Consistency Theorem

## CAP

The CAP theorem states that it is impossible for a distributed computing system to simultaneously provide all three of the following guarantees:
- **Consistency:** all nodes see the same data at the same time.
- **Availability:** a guarantee that every request receives a response about whether it was successful or failed.
- **Partition Tolerance:** the system continues to operate despite arbitrary message loss or failure of part of the system.

You can't have three out of the three, and essentially have to settle for two out of the three. In particular, the CAP theorem implies that in the presence of a network partition, one has to choose between consistency and availability.

When choosing consistency over availability, the system will return an error or a time-out if particular information cannot be guaranteed to be up to date due to network partitioning. 

When choosing availability over consistency, the system will always process the query and try to return the most recent available version of the information, even if it cannot guarantee it is up to date due to network partitioning.

## BASE

BASE = **B**asically **A**vailable + **S**oft State + **E**ventual Consistency. 

It is a data system design philosophy that prizes availability over consistency of operations. BASE was developed as an alternative for producing more scalable and affordable data architectures, providing more options to expanding enterprises/IT clients and simply acquiring more hardware to expand data operations.

BASE may be explained in contrast to another design philosophy - Atomicity, Consistency, Isolation, Durability (ACID). The ACID model promotes consistency over availability, whereas BASE promotes availability over consistency.

Consistency is not a big requirement and is not looked as a MUST for the success of business goals. BASE is actually a result of applying CAP in a certain manner as desired by the requirements of a distributed system.

# Consistency Models

**Strong Consistency:** is a requirement that upon update operations all nodes agree on the new value before making the new value visible to clients. Strong Consistency offers up-to-date data but at the cost of *high latency*.

**Weak Consistency:** is the opposite of strong consistency. It doesn't assure that the updated data will be visible from all the nodes simultaneously.

**Eventual Consistency:** is one kind of weak consistency. It doesn't assure that the updated data will be read  from all the nodes right away, but the data from all the nodes will be eventually consistent at last in a limited time. Eventual consistency offers *low latency* but inconsistent data in a short period of time.

# Typical Solutions

## 两/三阶段提交

xxxx

## TCC: Try-Confirm-Cancel 补偿模式+最终一致性

xxxx

## 消息事务+最终一直性

xxxx

# References

[1] 分布式系统事务一致性: [https://www.cnblogs.com/luxiaoxun/p/8832915.html](https://www.cnblogs.com/luxiaoxun/p/8832915.html)

[2] 常用的分布式事务解决方案介绍有多少种: [https://www.zhihu.com/question/64921387/answer/225784480](https://www.zhihu.com/question/64921387/answer/225784480)

[3] 谈谈分布式系统的CAP理论: [https://zhuanlan.zhihu.com/p/33999708](https://zhuanlan.zhihu.com/p/33999708)

[4] 微服务下的数据一致性的几种实现方式之概述: [https://www.jianshu.com/p/b264a196b177](https://www.jianshu.com/p/b264a196b177)

[5] 用消息队列和消息应用状态表来消除分布式事务: [https://my.oschina.net/picasso/blog/35306](https://my.oschina.net/picasso/blog/35306)

[6] xxxxx: []()










