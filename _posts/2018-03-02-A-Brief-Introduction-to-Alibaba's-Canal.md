---
layout: post
title: "A Brief Introduction to Alibaba's Canal"
date: 2018-03-02 13:26:51
categories: Middleware
permalink: /archivers/A-Brief-Introduction-to-Alibaba's-Canal
---

_(0000 words, 00 minutes)_

In the previous article, I introduced the deployment of our Elasticsearch cluster, which is used for a product retrieval system. In order to synchronize the data from MySQL database to our ES cluster in near real time, we investigated many middle wares, and finally chose the _Canal_ as our solution. _Canal_ is a data synchronization middle ware, which is one of Alibaba's open source projects. The way it works is that it disguises itself as the slave database and ingests data from a real master database. By parsing the binary logs, it re-plays the events happened in the master database, and pushes the events in the form of messages to its downstream.  

In this article, I will firstly introduce the background, then the general structure of _Canal_, and in the end I will talk about its high availability (HA) design. This article is entirely technical, anything related to commercial information has been excluded.  

<!--more-->

# The Background 

In the early days, Alibaba group has the demands of synchronizing data over geographical and temporal distances, due to their databases were deployed in Hangzhou and United States. Since 2010, Alibaba attempted to parse the binary logs of databases to synchronize data incrementally. This led to the birth of project _Canal_, and opened a new era.

- Language: Developed entirely in Java.
- Positioning: Currently supports MySQL, providing incremental data subscription and consumption.

# How Canal Works

![master-slave](https://github.com/ZhongyangMA/images/raw/master/alibaba-canal/master-slave1.jpg)

## The Data Synchronizing Process of MySQL

The process takes three steps:

1. The master records the changes of database to its _Binary Log_, these changes are called "binary log events".
2. The slave database copies the binary log events to its _Relay Log_.
3. The slave re-plays the events in its _Relay Log_, and adds these changes to its own data.

## The Binlog

The binlog in MySQL:

- The binlog of MySQL is stored in multiple files. Thus we need _binlog filename_ and _binlog position_ to locate one _Log Event_.
- Based on the way it was generated, the formats of MySQL's binlog are classified as three types, they are: statement-based, row-based and mixed.

Currently, _Canal_ supports all binlog formats when performing incremental subscription. But when it comes to data synchronization, only row-based format is appropriate, because the statement-based format doesn't contain any data at all.

## How Canal Works

![master-slave2](https://github.com/ZhongyangMA/images/raw/master/alibaba-canal/master-slave2.jpg)

The data synchronization of _Canal_ takes three steps:

1. _Canal_ disguises itself as the slave database and communicates with a real master database via dump protocol.
2. Once the master receives the dump requests, it pushes _binlogs_ to the slave, which is actually a _Canal_ server of course.
3. The _Canal_ server parses the _binlogs_, replays the events happened in the master database, and pushes the events in the form of messages to its downstream.

# The Architecture

![architecture.png](https://github.com/ZhongyangMA/images/raw/master/alibaba-canal/architecture.png)

As shown in this figure:

- A _Server_ is a running instance of _Canal_, which corresponds to one JVM.
- An _Instance_ in this figure corresponds to a data processing (synchronization) queue, and one _Canal_ server could contain 1 to n _Instances_. 
- In every _Instance_, there are three important components, they are: eventParser, eventSink and eventStore.
- The eventParser: disguises itself as the slave, parses _binlog_ and digests data from a real master database.
- The eventSink: links the _Parser_ and _Store_, performs the data processing, filtering, dispatching, etc.
- The eventStore: stores the data.

## The _eventStore_ Component
The design of a ring buffer:

![ringbuffer](https://github.com/ZhongyangMA/images/raw/master/alibaba-canal/ringbuff1.png)

The _eventStore_ is a circular message queue in the memory. It defines three cursor:

- Put: The last position where the message from _Sink_ module was written.
- Get: The last position where the message was subscribed.
- Ack: The last position where the message was successfully consumed.

_Canal_ allows to perform Get/Ack operations asynchronously. For example, you can invoke Get several times continuously, then invokes Ack or Rollback in sequence. This is called streaming API in _Canal_.

![stream-api](https://github.com/ZhongyangMA/images/raw/master/alibaba-canal/buffer2.jpg)

# The HA Mechanism

The High Availability (HA) mechanism of _Canal_ relies on Zookeeper. It contains two parts:

1. The HA of _Canal_ server: The instances are distinguished by their _destination_ names, if an instance in canal-server01 has the same _destination_ name with the instance in canal-server02, only one of them is allowed to run, in the meanwhile, the other one is in standby state.
2. The HA of _Canal_ client: One _Instance_ in the _Canal_ Server only allows to be consumed by one _Canal_ client simultaneously.

![HA.png](https://github.com/ZhongyangMA/images/raw/master/alibaba-canal/HA.png)

As shown in this figure, the procedure of starting up is: 

1. Canal-server01 and Canal-server02 will preemptively create the _Ephemeral Node_ in Zookeeper when they are attempting to start their own _Instances_ with the same _destination_ name. 
2. The one who successfully created the _Ephemeral Node_, will start running its instance; The one who didn't, will be in the standby status.
3. Once ZooKeeper finds the Canal-server who created the _Ephemeral Node_ breaks down, it will notify other Canal servers to repeat step 1, thus there will be another Canal-server starts to run.
4. The same as canal-server, only one canal-client will be allowed to run simultaneously. When the canal-client starts, it will ask the ZooKeeper which canal-server is running, then it will connects to the running canal-server.

Note: The life cycle of _Ephemeral Node_ in ZooKeeper only binds to its client's session. If ZooKeeper loses the session of a client, the corresponding _Ephemeral Node_ will be deleted automatically.

# References

[1] Alibaba's Canal official document: [https://github.com/alibaba/canal](https://github.com/alibaba/canal)  

