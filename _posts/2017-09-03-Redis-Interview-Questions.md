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

# Redis Persistence

Redis provides a different range of persistence options:

- The RDB persistence performs point-in-time **snapshots** of your dataset at specified intervals.
- The AOF persistence logs every write operation received by the server, that will be played again at server startup, reconstructing the original dataset.

**Snapshotting**: By default Redis saves snapshots of the dataset on disk, in a binary file called dump.rdb. You can configure Redis to have it save the dataset every N seconds if there are at least M changes in the dataset, or you can manually call the SAVE or BGSAVE commands. It works like this:

- Redis forks a child process.
- The child starts to write the dataset to a temporary RDB file.
- When the child is done writing the new RDB file, it replaces the old one.

**Append-only file**: Snapshotting is not very durable. If your computer running Redis stops, your power line fails, or you accidentally kill -9 your instance, the latest data written on Redis will get lost. The append-only file is an alternative, fully-durable strategy for Redis.

Once you turn on the AOF, every time Redis receives a command that changes the dataset (e.g. SET) it will append it to the AOF. When you restart Redis it will re-play the AOF to rebuild the state.

|              | RDB                                     | AOF                                                          |
| ------------ | --------------------------------------- | ------------------------------------------------------------ |
| advantage    | compact single file                     | more durable                                                 |
| disadvantage | may lose data written in latest minutes | the log file is bigger.  may be slower than RDB depending on the exact fsync policy. |

# Single Threaded

Redis is single threaded, it serializes the concurrent requests to a queue by NIO (epoll) techniques. Thus it avoids the switches between multiple threads, as a result, it gains a better performance.

# Replication

xxxxx

# References

[1] Top 10 Redis interview questions: [https://career.guru99.com/top-10-redis-interview-questions](https://career.guru99.com/top-10-redis-interview-questions)

[2] Redis持久化详解: [https://segmentfault.com/a/1190000012908434](https://segmentfault.com/a/1190000012908434)

[3] Redis Persistence: [https://redis.io/topics/persistence](https://redis.io/topics/persistence)

[4] 关于redis的主从、哨兵、集群: [https://blog.csdn.net/c295477887/article/details/52487621](https://blog.csdn.net/c295477887/article/details/52487621)

[5] Redis Replication: [https://redis.io/topics/replication](https://redis.io/topics/replication)



