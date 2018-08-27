---
layout: post
title: "Redis Interview Questions"
date: 2017-09-03 14:50:33
categories: Middleware Java
permalink: /archivers/Redis-Interview-Questions
---

Redis is an open-source, advanced key-value data store. It is also referred as a data structure server which keys not only contains strings, but also hashes, sets, lists, and sorted sets. Redis mostly used for cache solutions and session management. In this article, I record down some of the important points about Redis for the further review. 

<!--more-->

# Basic Features

Following are the main features of Redis:

- What is Redis: "Redis" stands for "REmote DIctionary Server", and it is written in ANSI C. It is extremely fast, persistent, portable and supports many languages such as C++, Java, PHP, etc. 
- The data structures: The data structures it supports include **string**, **list**, **hash**, **set** and **sorted set**.
- Redis is very fast: It is an in-memory key-value data store that can execute **10,0000** queries per second. Redis is very fast because it loads the whole dataset in primary memory.
- Replication feature: Redis supports simple master to slave replication. When a relationship is established, data from the master is transferred to the slave.
- The main limitation for Redis: is the size of the physical memory. therefore, it can't be used for data storing and searching at a very large scale.

# Redis vs. Memcached

Below are the main differences between Redis and Memcached:

| diffs           | Redis                                                        | Memcached                                                    |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| durability      | Redis not only does caching information in memory but also save data to disks for higher availability | Memcached only does caching information in memory            |
| eviction        | Redis supports several eviction strategies                   | In Memcached when they overflow memory, the one you have not used recently (LRU- least recently used) will get deleted |
| CAS             | Redis does not support CAS ( Check and Set). It is useful for maintaining cache consistency | Memcached supports CAS (Check and Set)                       |
| data structures | Redis has got stronger data structures; it can handle strings, binary safe strings, list of binary safe strings, sorted lists, etc. | In Memcached, you have to serialize the objects or arrays in order to save them and to read them back you have to un-serialize them. |
| key length      | Redis had a maximum of 2GB key length                        | Memcached had a maximum of 250 bytes length                  |
| single thread   | Redis is single threaded                                     | Memcached is multi-threaded                                  |

# The Eviction Strategy

What happens if Redis runs out of memory? 

You can use the "maxmemory" option in the config file to put a limit to the memory Redis can use. If this limit is reached Redis will start to reply with an error to write commands (but will continue to accept read-only commands), or you can configure it to evict keys when the max memory limit is reached.

The following lists the details of Redis' eviction policies:

- **allkeys-lru**: the service evicts the least recently used keys out of all keys.
- **allkeys-random**: the service randomly evicts keys out of all keys.
- **volatile-lru**: the service evicts the least recently used keys out of all keys with an "expire" field set.
- **volatile-random**: the service randomly evicts keys with an "expire" field set.
- **volatile-ttl**: the service evicts the shortest time to live keys (out of all keys with an "expire" field set).
- **no-eviction**: the service will not evict any keys and no writes will be possible until more memory is freed.

# Durability

xxxxx


# References

[1] Top 10 Redis interview questions: [https://career.guru99.com/top-10-redis-interview-questions](https://career.guru99.com/top-10-redis-interview-questions)

[2] xxxxx: []()

[3] xxxxx: []()

[4] xxxxx: []()

[5] xxxxx: []()



