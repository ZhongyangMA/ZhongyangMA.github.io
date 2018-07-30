---
layout: post
title: "Spring Transaction Management"
date: 2017-06-05 10:23:02
categories: Java Spring
permalink: /archivers/Spring-Transaction-Management
---

A database transaction is a sequence of actions that are treated as a single unit of work. These actions should either complete entirely or take no effect at all. Transaction management is an important part of RDBMS-oriented enterprise application to ensure data integrity and consistency. The concept of transactions can be described with the following four key properties described as **ACID**.

<!--more-->

**Atomicity** − A transaction should be treated as a single unit of operation, which means either the entire sequence of operations is successful or unsuccessful.

**Consistency** − This represents the consistency of the referential integrity of the database, unique primary keys in tables, etc.

**Isolation** − There may be many transaction processing with the same data set at the same time. Each transaction should be isolated from others to prevent data corruption.

**Durability** − Once a transaction has completed, the results of this transaction have to be made permanent and cannot be erased from the database due to system failure.

# Programmatic vs. Declarative

Spring supports both **programmatic** and **declarative** transaction management. Declarative transaction management is preferable over programmatic transaction management though it is less flexible than programmatic transaction management, which allows you to control transactions through your code. But as a kind of cross-cutting concern, declarative transaction management can be modularized with the AOP approach. Spring supports declarative transaction management through the Spring AOP framework.

# Spring Transaction Abstractions

The key to the Spring transaction abstraction is defined by the *org.springframework.transaction.PlatformTransactionManager* interface, which is as follows:

```java
public interface PlatformTransactionManager {
    TransactionStatus getTransaction(TransactionDefinition definition) throws TransactionException;
    void commit(TransactionStatus status) throws TransactionException;
    void rollback(TransactionStatus status) throws TransactionException;
}
```

## Propagation Behavior

The *TransactionDefinition* is the core interface of the transaction support in Spring and it is defined as follows:

```java
public interface TransactionDefinition {
    int getPropagationBehavior();
    int getIsolationLevel();
    String getName();
    int getTimeout();
    boolean isReadOnly();
}
```

The *getPropagationBehavior()* method returns the propagation behavior. Following are the possible values for propagation types:

- **TransactionDefinition.PROPAGATION_MANDATORY:** Supports a current transaction; throws an exception if no current transaction exists.
- **TransactionDefinition.PROPAGATION_NESTED:** Executes within a nested transaction if a current transaction exists.
- **TransactionDefinition.PROPAGATION_NEVER:** Does not support a current transaction; throws an exception if a current transaction exists.
- **TransactionDefinition.PROPAGATION_NOT_SUPPORTED:** Does not support a current transaction; rather always execute nontransactionally.
- **TransactionDefinition.PROPAGATION_REQUIRED:** Supports a current transaction; creates a new one if none exists.
- **TransactionDefinition.PROPAGATION_REQUIRES_NEW:** Creates a new transaction, suspending the current transaction if one exists.
- **TransactionDefinition.PROPAGATION_SUPPORTS:** Supports a current transaction; executes non-transactionally if none exists.
- **TransactionDefinition.TIMEOUT_DEFAULT:** Uses the default timeout of the underlying transaction system, or none if timeouts are not supported.

## Isolation Level

Following are the possible values for isolation level:

- **TransactionDefinition.ISOLATION_DEFAULT:** This is the default isolation level, which is almost equivalent to TransactionDefinition.ISOLATION_READ_COMMITTED.

- **TransactionDefinition.ISOLATION_READ_UNCOMMITTED:** Indicates that dirty reads, non-repeatable reads, and phantom reads can occur.

- **TransactionDefinition.ISOLATION_READ_COMMITTED:** Indicates that dirty reads are prevented; non-repeatable reads and phantom reads can occur.

- **TransactionDefinition.ISOLATION_REPEATABLE_READ:** Indicates that dirty reads and non-repeatable reads are prevented; phantom reads can occur.

- **TransactionDefinition.ISOLATION_SERIALIZABLE:** Indicates that dirty reads, non-repeatable reads, and phantom reads are prevented.

| Isolation Types            | dirty read | non-repeatable read | phantom read |
| -------------------------- | ---------- | ------------------- | ------------ |
| ISOLATION_READ_UNCOMMITTED | -          | -                   | -            |
| ISOLATION_READ_COMMITTED   | prevented  | -                   | -            |
| ISOLATION_REPEATABLE_READ  | prevented  | prevented           | -            |
| ISOLATION_SERIALIZABLE     | prevented  | prevented           | prevented    |

**Dirty read:** xxx.

**Non-repeatable read:** xxx.

**Phantom read:** xxx.

# The *@Transactional* Annotation

xxxxx




# References

[1] 事务的传播行为: [http://blog.sina.com.cn/s/blog_4b5bc0110100z7jr.html](http://blog.sina.com.cn/s/blog_4b5bc0110100z7jr.html)

[2] Spring @Transactional 注解的详细用法: [https://www.cnblogs.com/yepei/p/4716112.html](https://www.cnblogs.com/yepei/p/4716112.html)

[3] Spring Framework Transaction Management: [https://docs.spring.io/spring-framework/docs/4.2.x/spring-framework-reference/html/transaction.html](https://docs.spring.io/spring-framework/docs/4.2.x/spring-framework-reference/html/transaction.html)

[4] Spring - Transaction Management: [https://www.tutorialspoint.com/spring/spring_transaction_management.htm](https://www.tutorialspoint.com/spring/spring_transaction_management.htm)




