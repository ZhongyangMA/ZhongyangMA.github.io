---
layout: post
title: "A Brief Introduction to Alibaba's Canal"
date: 2018-03-02 13:26:51
categories: Middleware
permalink: /archivers/A-Brief-Introduction-to-Alibaba's-Canal
---

_(0000 words, 00 minutes)_

In the previous article, I introduced the deployment of our Elasticsearch cluster, which is used for a product retrieval system. In order to synchronize the data from MySQL database to our product retrieval system in near real time, we have to find a solution. We investigated many data synchronization middle wares, and finally we chose the _Canal_ as our solution. _Canal_ is a data synchronization middle ware, which is one of Alibaba's open source projects. The way it works is that it disguises itself as the slave database and ingests data from a real master database. Through parsing the binary log, it re-plays the events happened in the master database, and pushes the events in the form of messages to the downstream. In this article, I will firstly introduce the general structure of _Canal_ and the brief process of the data synchronization of it, and in the end I will talk about its high availability (HA) design. 

This article is entirely technical, anything related to commercial information has been excluded.  

<!--more-->

# The Background 

xxxx