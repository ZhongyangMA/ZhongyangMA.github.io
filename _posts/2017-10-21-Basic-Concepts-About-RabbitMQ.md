---
layout: post
title: "Basic Concepts About RabbitMQ"
date: 2017-10-21 16:40:29
categories: Java Middleware
permalink: /archivers/Basic-Concepts-About-RabbitMQ
---

RabbitMQ is a message broker: it accepts and forwards messages. You can think about it as a post office: when you put the mail that you want posting in a post box, you can be sure that Mr. Postman will eventually deliver the mail to your recipient. In this analogy, RabbitMQ is a post box, a post office and a postman. In this article, I will give a brief introduction of it, including the basic concepts and the common situations where the MQ is needed.

<!--more-->

# The AMQP Protocol

The AMQP (Advanced Message Queuing Protocol) is one of the protocols supported by RabbitMQ. The AMQP is a messaging protocol that enables conforming client applications to communicate with conforming messaging middleware brokers.

The AMQP Model has the following view of the world: messages are published to **exchanges**, then exchanges distribute message copies to **queues** based on rules called **bindings**. Then AMQP brokers either deliver messages to consumers subscribed to queues, or consumers fetch/pull messages from queues on demand.

# Exchanges

*Exchanges* are AMQP entities where messages are sent. Exchanges take a message and route it into zero or more queues. The routing algorithm used depends on the *exchange type* and rules called *bindings*. AMQP 0-9-1 brokers provide four exchange types: Direct, Fanout, Topic, Headers.

Exchanges can be durable or transient. Durable exchanges survive broker restart whereas transient exchanges do not (they have to be redeclared when broker comes back online). Not all scenarios and use cases require exchanges to be durable.

## Default Exchange

The default exchange is a **direct exchange** with no name (empty string) pre-declared by the broker.

> channel.exchangeDeclare(""[exchange name], BuiltinExchangeType.DIRECT);

It has one special property that makes it very useful for simple applications: every queue that is created is automatically bound to it with a **routing key** which is the same as the **queue name**.

> channel.basicPublish(""[exchange name], QUEUE_NAME[routing key], null, message.getBytes("UTF-8"));

The default exchange makes it seem like it is possible to deliver messages directly to queues, even though that is not technically what is happening.

## Direct Exchange

A direct exchange delivers messages to queues based on the message routing key. A direct exchange is ideal for the unicast routing of messages (although they can be used for multicast routing as well). Here is how it works: 

- A queue binds to the exchange with a routing key K

  > channel.queueBind(queue_name, exchange_name, routing_key);

- When a new message with routing key R arrives at the direct exchange, the exchange routes it to the queue if K = R

  > channel.basicPublish(exchange_name, routing_key, null, null);

Direct exchanges are often used to distribute tasks between multiple workers (instances of the same application) in a round robin manner. When doing so, it is important to understand that, in AMQP 0-9-1, messages are **load balanced between consumers but not between queues**.

## Fanout Exchange

A fanout exchange routes messages to all of the queues that are bound to it and the routing key is ignored. 

> channel.queueBind(queue_name, exchange_name, ""[routing key is ignored]);

If N queues are bound to a fanout exchange, when a new message is published to that exchange a copy of the message is delivered to all N queues. Fanout exchanges are ideal for the broadcast routing of messages.

## Topic Exchange

Topic exchanges route messages to one or many queues based on matching between a message routing key and the pattern that was used to bind a queue to an exchange.

> channel.queueBind(queue_name, exchange_name, "\*.orange.\*"[binding key]);

Topic exchanges have a very broad set of use cases. Whenever a problem involves multiple consumers/applications that selectively choose which type of messages they want to receive, the use of topic exchanges should be considered.

## Headers Exchange

xxxxx (draft)

# Queues

xxxxx

# Title2

xxxxx

# Code Example

I created a repository to store the example code presented above, please visit:

**[https://github.com/ZhongyangMA/rabbitmq-client-examples](https://github.com/ZhongyangMA/rabbitmq-client-examples)**



# References

[1] AMQP 0-9-1 Model Explained: [https://www.rabbitmq.com/tutorials/amqp-concepts.html](https://www.rabbitmq.com/tutorials/amqp-concepts.html)

[2] AMQP协议简介: [https://blog.csdn.net/mx472756841/article/details/50815895](https://blog.csdn.net/mx472756841/article/details/50815895)

[3] RabbitMQ与AMQP协议详解: [https://www.cnblogs.com/frankyou/p/5283539.html](https://www.cnblogs.com/frankyou/p/5283539.html)

[4] xxxxx: []()

[5] xxxxx: []()








