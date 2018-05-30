---
layout: post
title:  "An Overview of Reactive Programming"
date:   2018-04-11 19:10:10
categories: Java Reactive
permalink: /archivers/An-Overview-of-Reactive-Programming
---

If you are following the Java community, you may notice that the reactive programming is not something new, it's been around for a while. But in these years, it seems to be getting more and more popular. Recently, Java community released many new version tools on this theme, which can be confusing sometimes.  

In this article, we firstly pick some of the new updates and sort out their relationship, then we walk through some of the basic concepts about reactive programming, and finally we provide some example codes which could help you get a better understanding about this topic. 

<!--more-->

# New Version Tools

## Spring Boot 2.0

Spring Boot 2.0 are now offering first-class support for developing reactive applications, via auto-configuration and starter-POMs. [1] The configuration of Spring Boot is as simple as always, we could finish the setup of a new application only after just a few configurations. 

The main updates related to reactive are：  

- built based on Spring Framework 5, which includes the new module WebFlux
- Netty is embedded as the default web server, supports reactive applications
- WebFlux runs on Netty by default


## Spring Framework 5

New spring-webflux module, an alternative to spring-webmvc built on a reactive foundation, intended for use in an event-loop execution model. [2] The most important update is the introducing of WebFlux, executing in an event-loop model.

The main updates are:

- dependency: baseline Java 8, compatible with Java 9
- infrastructures supporting reactive
- adapters for runtime such as Netty
- new module WebFlux (Reactor 3.x is embedded)


## Java 9 Reactive Stream

In the age of Java 8, the Reactive Stream API has already existed. It is available as a separate jar:

```xml
<dependency>
    <groupId>org.reactivestreams</groupId>
    <artifactId>reactive-streams</artifactId>
</dependency>
```

In Java 9, Reactive Streams is officially part of the Java API. [3]

The main updates is:

- Reactive Stream API（java.util.concurrent.Flow）[4]


The Reactive Stream API is a set of interfaces, which is only a specification and has no implementations. There are several popular products that have implemented Reactive Stream API, such as RxJava, Reactor, akka, etc. And the Reactor 3.x is the framework which is embedded in Spring WebFlux.

Therefore currently Spring Framework 5.x provides two development stacks:

1. **Spring WebFlux:** based on Reactive Stream API，runs on servlet 3.1+ container（Tomcat 8）or Netty, these web servers support the NIO or Reactor model
2. **Spring MVC:** based on traditional Servlet API，runs on traditional Servlet, one-request-per-thread, blocking IO

The picture below is a comparison of the two stacks:

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/two-stacks.png)
Please note that the JDBC still don't support the Reactive asynchronous invocation. If we invoke functions which get data from MySQL, the thread will be blocked until the data returns.

# What is Reactive Programming

In order to figure out what the Reactive Programming is, we need to have a look at the history of IO and understand what happened in the past. We will start from the concept of classical IO model.

## Five IO models in Linux

Five classical IO models in Linux:

1. Blocking IO
2. Non-blocking IO
3. IO multiplexing
4. Signal driven I/O
5. Asynchronous I/O

The first 4 are synchronous, only the last one is asynchronous IO.

### Blocking IO

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/io1.jpg)

As shown in the figure, the IO process takes two steps: (1) kernel data preparation (2) copying the data from kernel space to user space. Once the user thread invokes the IO method, the user thread will be blocked and has to wait until the data is returned. This is the blocking IO model.

### Non-blocking IO

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/io2.jpg)

In the non-blocking IO model, after the IO function is invoked and before the kernel data is ready, the IO function could return immediately. The user thread calls the IO function many times (polling), once it finds the data is ready, it will take the second step: copying data from kernel space to user space, and in this step the user thread is blocked.

### IO multiplexing

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/io3.jpg)

The user thread invokes methods like select, poll or epoll, to monitor multiple IO requests, it will be blocked until at least one IO request is ready (ready to read or write). The IO multiplexing model could process multiple IO requests by just one thread.

### Signal driven I/O

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/io4.jpg)

Once the user thread invokes the IO function, it will return immediately even though the data is not ready. When the kernel data is ready, a signal function will be sent, to notify the user thread to carry on the second step, copying data from the kernel space to the user space.

### Asynchronous I/O

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/io5.jpg)

In the asynchronous I/O model, the user thread will not be blocked in both of the two steps in IO operation. When the copying of data is finished in the second step, a signal function will be sent, to notify the user thread that the IO operation is complete.

## Comparison between the five IO models

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/io6.jpg)

The comparison between the five IO models is shown in this figure. [5]

## Four IO models in Java

Except the signal driven IO model, other four IO models are all supported in Java. [6]\[7]

1. the earliest traditional IO in Java is the blocking IO
2. the Java NIO is the non-blocking IO
3. the Reactor pattern in Java NIO is an implementation of IO multiplexing
4. the Proactor pattern in Java AIO is an implementation of asynchronous IO

The traditional IO in Java is blocking IO, for example, if you want to read data from a socket and invoke read() method, the thread will get stuck at read() method until the data is returned. Therefore in the traditional web service design, the multi-thread (or thread pool) pattern are widely adopted. A thread is blocked when it serves one request, if another request comes at this moment, we have to start a new thread to serve it. The blockage in one thread shouldn't affect the works in other threads. This kind of server design seems straightforward, but it starts thread for every request, which will be resource-consuming, even though the thread pool pattern makes the thread reusable.

The multi-thread (or thread pool) pattern are suitable in the scenario that most of the connections are short connections. If most of the connections are long connections, and simultaneously there're only a few connections have IO operations, serving every connection with one thread will be quite wasteful. [8]\[9] Therefore the IO model with higher performance appears, the Java NIO model. It was designed on the basis of the Reactor pattern, which is also the most important concept in Reactive Programming.

## Java NIO Model

Let's walk through some concepts in Java NIO first: Channel, Buffer and Selector.

### Channel

Channel is similar to Stream in traditional IO model. But Streams are unidirectional, you use InputStream to read or use OutputStream to write. Channels can be bidirectional, you can read and write through one Channel.

Some commonly used Channels are listed below:

- FileChannel - read and write through file
- SocketChannel - read and write through TCP socket
- ServerSocketChannel - Listen the TCP connection from clients, create new SocketChannel for each TCP connection
- DatagramChannel - read and write through UDP protocol

### Buffer

Buffer can be considered as a data container, which could be implemented by an array. Either reading from a channel or writing to a channel, data must be put into the buffer first.

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/buffer.jpg) 

This figure shows how the data is passed from the client-side to the server-side in Java NIO.

### Selector

Selector is the core class in Java NIO, it monitors and processes interested IO events that happened in multiple registered Channels. Through this mechanism, we could maintain multiple connections by just one thread. Only when the IO events actually happen among these connections, the IO process logic are truly invoked. There's no need to start a new thread each time when a new connection comes in, therefore it would significantly reduce the system load.

In company with Selector, the SelectionKey is another important class, which represents an arrived event. These two classes constitute the key logic of the NIO server. [10]

The figure below shows the monitoring of multiple channels (or connections) by a Selector running on a single thread.

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/selector.jpg)

The code below shows how to create a Selector and the events that can be set: [11]

```java
Selector selector = Selector.open();  // create Selector
SelectionKey key = channel.register(selector, Selectionkey.OP_READ);   // register the channel to Selector
/*
The second parameter in register() specifies the interested events, which are:
Connect - ready to connect
Accept - ready to accept
Read - ready to read
Write - ready to write
The corresponding status checking methods are:
*/
selectionKey.isConnectable();
selectionKey.isAcceptable();
selectionKey.isReadable();
selectionKey.isWritable();
```

## Java NIO Example Code

Now let's get a further understanding about these concepts with some example codes. [12]\[13]

```java
// create Selector
Selector selector = Selector.open();
// create ServerSocketChannel and bind it to the port you want
ServerSocketChannel server = ServerSocketChannel.open();
server.bind(new InetSocketAddress("127.0.0.1", 1234));
// set ServerSocketChannel as non-blocking
server.configureBlocking(false);
// register the server channel to the selector and set the OP_ACCEPT as the interested event
server.register(selector, SelectionKey.OP_ACCEPT);
while (true) {
    // selector is blocked by select()
    // select() will put the registered events into SelectionKeys (and will not remove items automatically)
    if (selector.select() == 0) {
        continue;
    }
    // get the SelectionKey Set, each Selectionkey corresponds to a registered Channel
    Set<SelectionKey> keys = selector.selectedKeys();
    for (SelectionKey key : keys) {
        if (key.isAcceptable()) {
	    // get the SocketChannel connected to the client
	    SocketChannel channel = ((ServerSocketChannel) key.channel()).accept();
	    // set the channel to non-blocking as well
	    channel.configureBlocking(false);
            // could send messages to client here  
            channel.write(ByteBuffer.wrap(new String("Sent a message to client!").getBytes()));  
            // register the client channel to selector and set the OP_READ as the interested event
            channel.register(this.selector, SelectionKey.OP_READ);  
	}
	// if the channel has data to be read
	if (key.isReadable()) {
	    SocketChannel channel = (SocketChannel) key.channel();
	    // create buffer for reading
            ByteBuffer buffer = ByteBuffer.allocate(10);  
            channel.read(buffer);
	}
	// since the select() only increases the SelectionKey Set, we need to remove the key manually here
	keys.remove(key);
    }
}
```

## Reactor Pattern

The code example shows the simplest Reactor pattern. Now let's have a look at the summary of Reactor pattern by Doug Lea. According to his classification there're three types of Reactor pattern, they are: Single thread version, Multi-threaded version and Multi-reactors version. [14]\[15]

### Basic version (Single threaded)

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/reactor1.png)

As shown in the figure, in Single thread version, all the IO operations are processed by one NIO thread.

This NIO thread is the Reactor and also the Acceptor, responsible for listening multiple socket connections and dispatching and processing all the requests. This version is quite simple and straightforward, however in some high concurrency or high load scenario, it may not be appropriate.

### Multi-threaded version

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/reactor2.png)

The figure shows the multi-threaded version of Reactor pattern.

In this version, there is a specific thread which is responsible for listening and dispatching the connections. The IO operations are done by a NIO thread pool, which contains a queue of tasks and N available threads.

In most cases, the multi-threaded version is enough for the performance requirements. But in higher load scenario, it might be hard to meet the needs. Thus, there is an advanced version below: the multi-reactors version.

### Multi-reactors version

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/reactor3.png)

The figure shows the Multi-reactors version of Reactor pattern.

In this version, the Reactor has multiple parts. The main-Reactor is responsible for listening connections, creating new channels and dispatching them to the sub-Reactors. The sub-Reactor is responsible for polling the channels and invoking the worker thread pool to execute the IO operations.

## The Key Concept of Reactive Programming

Based on the information above, in this section, we will have a further discussion about some key concepts of Reactive Programming. [16]\[17]

### Reactive Programming vs. Reactive Streams

Reactive programming is not something new, it's just a programming paradigm and has existed for a while. Just like object-oriented programming, functional programming and procedural programming, reactive programming is just another programming paradigm.

Reactive Streams, on the other hand, is a specification. For Java programmers, Reactive Streams is an API. Reactive Streams gives us a common API for Reactive Programming in Java. [17]

### Key Attributes of Reactive Programming

The Reactive Manifesto [16] describes four key attributes of reactive systems: Responsive, Resilient, Elastic and Message Driven.

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/manifesto.png)

- **Responsive:** The system responds in a timely manner if at all possible. Responsive systems focus on providing rapid and consistent response times, establishing reliable upper bounds so they deliver a consistent quality of service.
- **Resilient:** The system stays responsive in the face of failure. Isolating components from each other ensures that parts of the system can fail and recover without compromising the system as a whole.
- **Elastic:** The system stays responsive under varying workload. Reactive Systems can react to changes in the input rate by increasing or decreasing the resources allocated to service these inputs.
- **Message Driven:** Reactive Systems rely on asynchronous message-passing to establish a boundary between components that ensures loose coupling, isolation and location transparency.

The first three attributes (Responsive, Resilient, Elastic) are more related to your architecture choices. It’s easy to see why technologies such as microservices, Docker and Kubernetes are important aspects of Reactive systems. As developers, it’s the last attribute, the Message Driven attribute, that interests us most. A few aspects of the Reactive Manifesto that do interest us are: **failures at messages**, **back-pressure** and **non-blocking**.

**Failures at messages:** Often in Reactive programming, you will be processing a stream of messages. What is undesirable is to throw an exception and end the processing of the stream of messages. The preferred approach is to gracefully handle the failure. In Reactive Steams, exceptions are first-class citizens. Exceptions are not rudely thrown. Error handling is built right into the Reactive Streams API specification.

**Back-pressure:** Have you ever heard of the phrase “Drinking from the firehose”? John Thompson gives a very vivid metaphor in his article What Are Reactive Streams in Java? [17]

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/back-pressure.png)

Back pressure is a very important concept in Reactive programming. It gives downstream clients a way to say: "I’d some more, please." Throttling is done programmatically rather than blocking threads.

**Non-blocking:** Non-blocking is another important aspect of reactive system. John Thompson takes the Node.js Server as the example and compares it to the traditional Java multi-threaded server.

In Node.js, there is a non-blocking event loop. Requests are processed in a non-blocking manner. Threads do not get stuck waiting for other processes.

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/nodejs-server.png)

Contrast the Node.js model to the typical multi-threaded server used in Java. Concurrency is achieved through the use of multiple threads.

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/multithreads-server.png)
John Thompson envisions the difference between the two approaches as the difference between a super highway and lots of city streets with lights: "With a single thread event loop, your process is cruising quickly along on a super highway. In a Multi-threaded server, your process is stuck on city streets in stop and go traffic."

### Introduction of Reactive Stream API

Let's take a look at Reactive Stream API briefly. It provides only four interfaces.

```java
// Publisher：is a provider of a potentially unbounded number of sequenced elements, publishing them according to the demand received from its Subscribers.
public interface Publisher<T> {
    public void subscribe(Subscriber<? super T> s);
}
// Subscriber：will receive calls to Subscriber.onSubscribe(Subscription) once after passing an instance of Subscriber to Publisher.subscribe(Subscriber).
public interface Subscriber<T> {
    public void onSubscribe(Subscription s);
    public void onNext(T t);
    public void onError(Throwable t);
    public void onComplete();
}
// Subscription：represents a one-to-one lifecycle of a Subscriber subscribing to a Publisher.
public interface Subscription {
    public void request(long n);
    public void cancel();
}
// Processor：represents a processing stage — which is both a Subscriber and a Publisher and obeys the contracts of both, usually used for transforming message types between publisher and subscriber.
public interface Processor<T, R> extends Subscriber<T>, Publisher<R> {
}
```

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/processor.png)

The picture below shows the typical interaction sequence between publisher and subscriber.

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/publisher-subscriber.png)

# Build WebFlux Application with Spring Boot 2.0

## Project Reactor

Reactive Streams API is only a set of interfaces and has no implementations. Project Reactor is one of the implementations. And the reactive functionality found in Spring Framework 5 is built upon Reactor 3.x. The reactive framework WebFlux relies on Reactor. [18]\[19]

Reactor has two important models, Flux<T> and Mono<T>, providing non-blocking asynchronous processing functionality with back-pressure. When the messages are produced slowly, the stream works in the push mode; When the messages are produced quickly, the stream will turns in pull mode. The Flux carries 0 to N events, it used for processing messages in a stream asynchronously. The Mono could trigger at most one event, it usually used as a notifier at the moment when the asynchronous task is finished. The Flux and the Mono can be converted to each other. Performing a counting operation on a Flux stream, the result is a Mono<Long> object. Combining two Monos together, the result will be a Flux object. [20][21]  

### Examples of creating Flux

```java
// by static methods:
List<String> words = Arrays.asList("aa","bb","cc","dd");
Flux<String> listWords = Flux.fromIterable(words);    // create from iterable collection
Flux<String> justWords = Flux.just("Hello","World");  // specify the elements by 'just'
listWords.subscribe(System.out::println);
justWords.subscribe(System.out::println);
// using generate() method to generate a Flux:
// by invoking next(), complete() and error() of SynchronousSink, generate() generates the elements of Flux one by one synchronously. 
Flux.generate(sink -> {
    sink.next("Hello");
    sink.complete();
}).subscribe(System.out::println);
// using create() method to create a Flux sequence:
// different with generate(), create() uses FluxSink object, which could create more than one elements in one invocation asynchronously.
Flux.create(sink -> {
    for (int i = 0; i < 10; i++) {
        sink.next(i);
    }
    sink.complete();
}).subscribe(System.out::println);
```

### Examples of creating Mono

```java
// Besides Mono has a few methods the same with Flux, it also has its own static methods, such as:
Mono.fromSupplier(() -> "Hello").subscribe(System.out::println);
Mono.justOrEmpty(Optional.of("Hello")).subscribe(System.out::println);
Mono.create(sink -> sink.success("Hello")).subscribe(System.out::println);
```

### Stream operating methods

```java
Flux.range(1, 10).filter(i -> i % 2 == 0).subscribe(System.out::println);  // only output elements that satisfy filter condition
Flux.just("a", "b").zipWith(Flux.just("c", "d")).subscribe(System.out::println);  // zipWith combines elements in current Stream with elements in another Stream.
// reduce and reduceWith perform a reduce operation, which will result in an output in the form of Mono.
Flux.range(1, 100).reduce((x, y) -> x + y).subscribe(System.out::println);
Flux.range(1, 100).reduceWith(() -> 100, (x, y) -> x + y).subscribe(System.out::println);
// etc.
```

### Message Processing

```java
// When need to process the messages in Flux or Mono, we could use subscribe() to add corresponding operations, for example:
// using subscribe() to handle normal and error messages:
Flux.just(1, 2)
        .concatWith(Mono.error(new IllegalStateException()))
        .subscribe(System.out::println, System.err::println);

// when error occurs, return default value:
Flux.just(1, 2)
        .concatWith(Mono.error(new IllegalStateException()))
        .onErrorReturn(0)
        .subscribe(System.out::println);

// when error occurs, use another stream:
Flux.just(1, 2)
        .concatWith(Mono.error(new IllegalStateException()))
        .switchOnError(Mono.just(0))
        .subscribe(System.out::println);

// when error occurs, retry it:
Flux.just(1, 2)
        .concatWith(Mono.error(new IllegalStateException()))
        .retry(1)
        .subscribe(System.out::println);
```

The section above only lists a few basic operating methods of Reactor. For more details please check the official documents. [22]

## WebFlux Module

WebFlux is a new module which is introduced in Spring Framework 5. It supports reactive HTTP and WebSocket.

Spring WebFlux provides a choice of two programming models on the reactive foundation:

- **Annotated Controllers:** consistent with Spring MVC, and based on the same annotations from the spring-web module.
- **Functional Endpoints:** lambda-based, lightweight, functional programming model. Think of this as a small library or a set of utilities that an application can use to route and handle requests.

![](https://github.com/ZhongyangMA/images/raw/master/webflux-streaming-demo/two-route-forms.png)

### Example codes

```xml
// 1. firstly, introduce the parent:
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.0.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>

// 2. introduce WebFlux dependency:
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```

```java
// 3. the entrance is the same with ordinary Spring Boot application:
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}

// 4. Functional style route:
@Configuration
public class Routes {
    @Bean
    public RouterFunction<?> routerFunction() {
        return route(
            GET("/api/city").and(accept(MediaType.APPLICATION_JSON)), cityService:: findAllCity
        ).and(route(
            GET("/api/user/{id}").and(accept(MediaType.APPLICATION_JSON)), cityService:: findCityById)
        );
    }
}

// 5. Annotated Controller style route:
@RestController
public class ReactiveController {
    @GetMapping("/hello_world")
    public Mono<String> sayHelloWorld() {
        return Mono.just("Hello World");
    }
}
```

Reactive Controller operates the asynchronous ServerHttpRequest and ServerHttpResponse, rather than HttpServletRequest and HttpServletResponse in Spring MVC. Mono and Flux are asynchronous, the function returns the reference of Flux or Mono object immediately even though the data is not ready. By polling channels, web server will find and process it when the data in Flux or Mono is ready. [23]\[24]

### Connect to reactive MongoDB

Spring Data Reactive Repositories currently supports Mongo, Cassandra, Redis, Couchbase. In this section we take mongoDB as an example to show the usage of reactive DB.

```xml
// 1.firstly, introducing the reactive MongoDB
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-mongodb-reactive</artifactId>
</dependency>

// 2.Config MongoDB in application.yml
spring:
  data:
    mongodb:
      uri: mongodb://localhost:27017/dbname
```

```java
// 3.Write a User entity, including fields like: id, name, gender etc.
// codes omitted

// 4.In dao layer, inherit ReactiveMongoRepository interface
public interface UserRepository extends ReactiveMongoRepository<User, String> {
    Flux<User> findByGender(String gender);  // function name will automatically match the corresponding field in User 
}

// 5.In the form of annotated Controller
// Handler layer
public Flux<User> findByGender(String gender) {
    return userRepository.findByGender(gender);
}
// Controller layer
@GetMapping(value = "/user/{gender}")
public Flux<User> findByGender(@PathVariable("gender") String gender) {
    return exampleHandler.findByGender(gender);
}

// 6.In the form of functional Router
// Handler layer
public Mono<ServerResponse> findByGender(ServerRequest request) {
    return ServerResponse.ok()
        .contentType(MediaType.APPLICATION_JSON)
        .body(userRepository.findByGender(request.pathVariable("gender")), User.class);
}
// Router layer
@Bean
public RouterFunction<ServerResponse> findByGender(ExampleHandler exampleHandler) {
    return RouterFunctions.route(RequestPredicates
        .GET("/functional/user/{gender}")
        .and(RequestPredicates.accept(MediaType.APPLICATION_JSON)), exampleHandler::findByGender);
}
```

As shown in the example code, in dao layer our repo interface takes the reactive types (Flux, Mono) as the return type.

The reactive data exchange capability of MongoDB entirely relies on the reactive MongoDB driver, which implemented the Reactive Streams API, providing non-blocking asynchronous Stream processing functionality with back-pressure.



**For more detailed code examples, please check:**

**[https://github.com/ZhongyangMA/webflux-streaming-demo](https://github.com/ZhongyangMA/webflux-streaming-demo)**



# References

[1] Spring Boot 2.0 Release Notes: [https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Release-Notes](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Release-Notes)  
[2] What's New in Spring Framework 5.x: [https://github.com/spring-projects/spring-framework/wiki/What%27s-New-in-Spring-Framework-5.x](https://github.com/spring-projects/spring-framework/wiki/What%27s-New-in-Spring-Framework-5.x)  
[3] Reactive Programming vs. Reactive stream: [https://www.tuicool.com/articles/UZfMB3M](https://www.tuicool.com/articles/UZfMB3M)  
[4] Reactive Programming with JDK 9 Flow API: [https://community.oracle.com/docs/DOC-1006738](https://community.oracle.com/docs/DOC-1006738)  
[5] Socket IO 模型: [https://blog.csdn.net/qq_23100787/article/details/60751795](https://blog.csdn.net/qq_23100787/article/details/60751795)  
[6] Java I/O 模型从 BIO 到 NIO 和 Reactor 模式: [http://www.jasongj.com/java/nio_reactor/](http://www.jasongj.com/java/nio_reactor/)  
[7] Java NIO、BIO、AIO 全面剖析: [https://blog.csdn.net/jiaomingliang/article/details/47684713](https://blog.csdn.net/jiaomingliang/article/details/47684713)  
[8] Java NIO：浅析I/O模型: [http://www.cnblogs.com/dolphin0520/p/3916526.html](http://www.cnblogs.com/dolphin0520/p/3916526.html)  
[9] Java IO：BIO和NIO区别及各自应用场景: [https://blog.csdn.net/jiyiqinlovexx/article/details/51204726](https://blog.csdn.net/jiyiqinlovexx/article/details/51204726)  
[10] Java NIO：NIO概述: [http://www.cnblogs.com/dolphin0520/p/3919162.html](http://www.cnblogs.com/dolphin0520/p/3919162.html)  
[11] JAVA nio selector 入门: [http://www.open-open.com/lib/view/open1452776436495.html](http://www.open-open.com/lib/view/open1452776436495.html)  
[12] Java NIO Socket通信: [http://www.open-open.com/lib/view/open1343002247880.html](http://www.open-open.com/lib/view/open1343002247880.html)  
[13] Java NIO 代码示例: [https://blog.csdn.net/u013967628/article/details/77841327](https://blog.csdn.net/u013967628/article/details/77841327)  
[14] Scalable IO in Java (Doug Lea): [http://gee.cs.oswego.edu/dl/cpjslides/nio.pdf](http://gee.cs.oswego.edu/dl/cpjslides/nio.pdf)  
[15] Netty（RPC高性能之道）原理剖析: [https://blog.csdn.net/zhiguozhu/article/details/50517551](https://blog.csdn.net/zhiguozhu/article/details/50517551)  
[16] The Reactive Manifesto: [https://www.reactivemanifesto.org/](https://www.reactivemanifesto.org/)  
[17] What Are Reactive Streams in Java: [https://dzone.com/articles/what-are-reactive-streams-in-java](https://dzone.com/articles/what-are-reactive-streams-in-java)  
[18] Project Reactor 官网: [http://projectreactor.io/](http://projectreactor.io/)  
[19] Reactor 实例解析: [http://www.open-open.com/lib/view/open1482286087274.html](http://www.open-open.com/lib/view/open1482286087274.html)  
[20] 使用 Reactor 进行反应式编程: [https://www.ibm.com/developerworks/cn/java/j-cn-with-reactor-response-encode/index.html](https://www.ibm.com/developerworks/cn/java/j-cn-with-reactor-response-encode/index.html)  
[21] Java ProjectReactor 框架之 Flux 篇: [https://www.jianshu.com/p/8cb66e912641](https://www.jianshu.com/p/8cb66e912641)  
[22] Reactor 3 Reference Guide: [http://projectreactor.io/docs/core/release/reference/docs/index.html](http://projectreactor.io/docs/core/release/reference/docs/index.html)  
[23] 聊聊 Spring Boot 2.0 的 WebFlux: [https://www.bysocket.com/?p=1987](https://www.bysocket.com/?p=1987)  
[24] 使用 Spring 5 的 WebFlux 开发反应式 Web 应用: [https://www.ibm.com/developerworks/cn/java/spring5-webflux-reactive/index.html](https://www.ibm.com/developerworks/cn/java/spring5-webflux-reactive/index.html)  

  

  