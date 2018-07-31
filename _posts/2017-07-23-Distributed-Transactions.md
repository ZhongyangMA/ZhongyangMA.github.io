---
layout: post
title: "Distributed Transactions"
date: 2017-07-23 19:21:46
categories: microservice architecture
permalink: /archivers/Distributed-Transactions
---

Local transactions are specific to a single transactional resource like a JDBC connection, whereas global transactions can span multiple transactional resources like transaction in a distributed system. A distributed or a global transaction is executed across multiple systems, and its execution requires coordination between the global transaction management system and all the local data managers of all the involved systems.

<!--more-->

# 一致性理论

## CAP理论

xxxxx

## BASE理论

xxxxx

# 一致性模型

强一致性

弱一致性

最终一致性

# 典型方案

## 两/三阶段提交

xxxx

## TCC: Try-Confirm-Cancel 补偿模式+最终一致性

xxxx

## 消息事务+最终一直性

xxxx

# References

[1] 分布式系统事务一致性: [https://www.cnblogs.com/luxiaoxun/p/8832915.html](https://www.cnblogs.com/luxiaoxun/p/8832915.html)

[2] 常用的分布式事务解决方案介绍有多少种: [https://www.zhihu.com/question/64921387/answer/225784480](https://www.zhihu.com/question/64921387/answer/225784480)

[3] xxxxx: []()






