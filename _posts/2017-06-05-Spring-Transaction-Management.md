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
- **TransactionDefinition.PROPAGATION_REQUIRED:** Supports a current transaction; creates a new one if none exists.(**default**)
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

**Dirty read:** occurs when one transaction reads data that has been written but not yet committed by another transaction. If the changes are later rolled back, the data obtained by the first transaction will be invalid.

**Non-repeatable read:** happens when a transaction performs the same query two or more times and each time the data is different. This is usually due to another concurrent transaction updating the data between the queries.

**Phantom read:** similar to nonrepeatable reads. These occur when a transaction reads several rows, and then a concurrent transaction inserts rows. Upon subsequent queries, the first transaction finds additional rows that were not there before.

# The *@Transactional* Annotation

In addition to the XML-based declarative approach to transaction configuration, you can use an annotation-based approach. Declaring transaction semantics directly in the Java source code puts the declarations much closer to the affected code.

You can place the *@Transactional* annotation before an interface definition, a method on an interface, a class definition, or a **public** method on a class. If you do annotate protected, private or package-visible methods with the *@Transactional* annotation, no error is raised, but the annotated method does not exhibit the configured transactional settings.

However, the mere presence of the *@Transactional* annotation is not enough to activate the transactional behavior. The *@Transactional* annotation is simply metadata that can be consumed by some runtime infrastructure that is *@Transactional*-aware and that can use the metadata to configure the appropriate beans with transactional behavior.

```xml
<tx:annotation-driven/>
    <bean id="transactionManager1" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        ...
        <qualifier value="order"/>
    </bean>
    <bean id="transactionManager2" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        ...
        <qualifier value="account"/>
    </bean>
```

```java
public class TransactionalService {
    @Transactional("order")
    public void setSomething(String name) { ... }
    
    @Transactional("account")
    public void doSomething() { ... }
}
```

This is the definition of @transactional annotation:

```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Transactional {
    @AliasFor("transactionManager")
    String value() default "";

    @AliasFor("value")
    String transactionManager() default "";

    Propagation propagation() default Propagation.REQUIRED;

    Isolation isolation() default Isolation.DEFAULT;

    int timeout() default -1;

    boolean readOnly() default false;

    Class<? extends Throwable>[] rollbackFor() default {};

    String[] rollbackForClassName() default {};

    Class<? extends Throwable>[] noRollbackFor() default {};

    String[] noRollbackForClassName() default {};
}
```

# Enable Transaction In Spring Boot

Applying transaction management in Spring Boot is simple and easy. You just need to turn on the transaction management by **@EnableTransactionManagement** annotation, and add **@Transactional** to methods.

Spring Boot will automatically inject the instances of *DataSourceTransactionManager* or *JpaTransactionManager* for *spring-boot-starter-jdbc* or *spring-boot-starter-data-jpa*, respectively. Both *DataSourceTransactionManager* and *JpaTransactionManager* have implemented the *PlatformTransactionManager* interface.

But sometimes we need to specify the transaction manager by ourselves if there exists several transaction managers in one application. Here is the example:

```java
@EnableTransactionManagement // which is equivalent to <tx:annotation-driven /> in xml
public class ProfiledemoApplication implements TransactionManagementConfigurer {
    @Resource(name="txManager2")
    private PlatformTransactionManager txManager2;
    // txManager1
    @Bean(name = "txManager1")
    public PlatformTransactionManager txManager1(DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }
    // txManager2
    @Bean(name = "txManager2")
    public PlatformTransactionManager txManager2(EntityManagerFactory factory) {
        return new JpaTransactionManager(factory);
    }
    // 实现接口 TransactionManagementConfigurer 方法，其返回值代表在拥有多个事务管理器的情况下默认使用的事务管理器
    @Override
    public PlatformTransactionManager annotationDrivenTransactionManager() {
        return txManager2;
    }
}
```

Then use the attribute *value* to specify the manager:

```java
@Service
public class XxxServiceImpl implements XxxService {
    // 使用value具体指定使用哪个事务管理器
    @Override
    @Transactional(value="txManager1")
    public void send() {
        // do something
    }
}
```

# References

[1] 事务的传播行为: [http://blog.sina.com.cn/s/blog_4b5bc0110100z7jr.html](http://blog.sina.com.cn/s/blog_4b5bc0110100z7jr.html)

[2] Spring @Transactional 注解的详细用法: [https://www.cnblogs.com/yepei/p/4716112.html](https://www.cnblogs.com/yepei/p/4716112.html)

[3] Spring Framework Transaction Management: [https://docs.spring.io/spring-framework/docs/4.2.x/spring-framework-reference/html/transaction.html](https://docs.spring.io/spring-framework/docs/4.2.x/spring-framework-reference/html/transaction.html)

[4] Spring - Transaction Management: [https://www.tutorialspoint.com/spring/spring_transaction_management.htm](https://www.tutorialspoint.com/spring/spring_transaction_management.htm)

[5] Spring Boot 事务的使用: [https://blog.csdn.net/catoop/article/details/50595702](https://blog.csdn.net/catoop/article/details/50595702)




