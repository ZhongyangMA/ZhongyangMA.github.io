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

# The Architecture

xxxx

# The HA Mechanism

xxxx

# References

[1] Alibaba's Canal official document: [https://github.com/alibaba/canal](https://github.com/alibaba/canal)  

