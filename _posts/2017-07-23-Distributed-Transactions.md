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

## 2-PC & 3-PC Protocols

The **2 Phase Commit** protocol referred to as XA (eXtended Architecture) arose. This protocol provides ACID-like properties for global transaction processing. It is a type of atomic commitment protocol in a distributed algorithm to coordinate all processes that participate in a distributed atomic transaction on if to commit or abort (rollback) the transaction. It consists of two phases:

1. **Voting phase:** A transaction coordinator requests all participating processes in DDBMS to vote a commit (yes) or an abort (no).
2. **Commit phase:** Based on the voting by all participating processes, the coordinator decides to commit only if all participating processes vote “yes” or abort if one or more of the participating processes votes “no”.

On the other hand, the **3-Phase Commit** protocol is a distributed algorithm that allows all participants in a distributed system to agree to commit a transaction or at least one process to abort the transaction by adding another phase **Pre-Commit** between Voting phase and Commit phase.

In 2PC protocol, if the coordinator fails permanently or dead, some participants in a distributed system will never resolve their transactions because after sending an agreement message to coordinator, they will block until a Commit or Rollback message sent from the coordinator. The 2PC technique is a blocking protocol. The 3PC protocol eliminates the 2PC protocol’s system blocking problem with the third phase **Pre-Commit**. If the coordinator fails before sending a preCommit message, other processes will unanimously (一致地) agree that the operation was aborted. The coordinator will not send out doCommit message until all processes have acknowledged.

The 2PC or 3PC is a synchronized algorithm for distributed transactions, it offers strong consistency but very high latency. 

## The TCC Protocol

xxxxTry-Confirm-Cancel 补偿模式+最终一致性,

## 消息事务+最终一直性

xxxx

# References

[1] 分布式系统事务一致性: [https://www.cnblogs.com/luxiaoxun/p/8832915.html](https://www.cnblogs.com/luxiaoxun/p/8832915.html)

[2] 常用的分布式事务解决方案介绍有多少种: [https://www.zhihu.com/question/64921387/answer/225784480](https://www.zhihu.com/question/64921387/answer/225784480)

[3] 谈谈分布式系统的CAP理论: [https://zhuanlan.zhihu.com/p/33999708](https://zhuanlan.zhihu.com/p/33999708)

[4] 微服务下的数据一致性的几种实现方式之概述: [https://www.jianshu.com/p/b264a196b177](https://www.jianshu.com/p/b264a196b177)

[5] 用消息队列和消息应用状态表来消除分布式事务: [https://my.oschina.net/picasso/blog/35306](https://my.oschina.net/picasso/blog/35306)

[6] xxxxx: []()










