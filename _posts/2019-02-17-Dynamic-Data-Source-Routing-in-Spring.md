---
layout: post
title:  "Dynamic Data Source Routing in Spring"
date:  2019-02-17 12:24:41
categories: Java Spring
permalink: /archivers/Dynamic-Data-Source-Routing-in-Spring
---

Recently we designed a system that needs to switch the data sources based on the region infos in each request. We accomplished that idea by adopting the AbstractRoutingDataSource, which is a very useful feature in spring framework if you want to choose a particular data source when user belongs to certain locale and switch to another data source if user belongs to another locale. In this post, I will show an example that using AbstractRoutingDataSource with a thread-bounded context to implement the dynamic data source routing.

<!--more-->

# The Source Code

The source codes of AbstractRoutingDataSource are listed below (only part of them):

```java
public abstract class AbstractRoutingDataSource extends AbstractDataSource implements InitializingBean {
    private Map<Object, Object> targetDataSources;
    private Object defaultTargetDataSource;
    private boolean lenientFallback = true;
    private DataSourceLookup dataSourceLookup = new JndiDataSourceLookup();
    private Map<Object, DataSource> resolvedDataSources;
    private DataSource resolvedDefaultDataSource;
    
    @Override
    public Connection getConnection() throws SQLException {
        return determineTargetDataSource().getConnection();
    }
    
    @Override
    public Connection getConnection(String username,String password) throws SQLException{
        return determineTargetDataSource().getConnection(username, password);
    }
    
    protected DataSource determineTargetDataSource() {
        Assert.notNull(this.resolvedDataSources, "DataSource router not initialized");
        Object lookupKey = determineCurrentLookupKey();
        DataSource dataSource = this.resolvedDataSources.get(lookupKey);
        if (dataSource == null && (this.lenientFallback || lookupKey == null)) {
            dataSource = this.resolvedDefaultDataSource;
        }
        if (dataSource == null) {
            throw new IllegalStateException("Cannot determine target DataSource for lookup key [" + lookupKey + "]");
        }
        return dataSource;
    }
    
    protected abstract Object determineCurrentLookupKey();
}
```

By definition, AbstractRoutingDataSource is an abstract data source implementation that routes getConnection() calls to one of various target DataSources based on a lookup key.

Thus the **determineCurrentLookupKey()** is the right method we're going to implement in the Datasource Router,  which is an extended class of AbstractRoutingDataSource.

# Datasource Router

We define our DataSource Router to extend the Spring AbstractRoutingDataSource. We implement the necessary determineCurrentLookupKey method to query our ContextHolder and return the appropriate key.

The AbstractRoutingDataSource implementation handles the rest of the work for us and transparently returns the appropriate DataSource.

```java
public class MultipleDataSource extends AbstractRoutingDataSource {
    @Override
    protected Object determineCurrentLookupKey() {
        return DynamicDataSourceHolder.getRouteKey();
    }
}
```

# Context Holder

The context holder implementation is a container that stores the current context as a ThreadLocal reference. In addition to holding the reference, it should contain static methods for setting, getting, and clearing it. AbstractRoutingDatasource will query the ContextHolder for the Context and will then use the context to look up the actual DataSource.

It’s critically important to use ThreadLocal here so that the context is bound to the currently executing thread.

It’s essential to take this approach so that behavior is reliable when data access logic spans multiple data sources and uses transactions.

```java
public class DynamicDataSourceHolder {
    private static ThreadLocal<String> routeKey = new ThreadLocal<String>();
    
    public static String getRouteKey() {
        String key = routeKey.get();
        return key;
    }

    public static void setRouteKey(String key) {
        routeKey.set(key);
    }
    
    public static void removeRouteKey() {
        routeKey.remove();
    }
}
```

# Configuration

We need a Map of contexts to DataSource objects to configure our AbstractRoutingDataSource. We can also specify a default DataSource to use if there is no context set.

```xml
<bean id="dataSource1" class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="net.sourceforge.jtds.jdbc.Driver">
    </property>
    <property name="url" value="jdbc:jtds:sqlserver://127.0.0.1;databaseName=test">
    </property>
    <property name="username" value="***"></property>
    <property name="password" value="***"></property>
</bean>
<bean id="dataSource2" class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="net.sourceforge.jtds.jdbc.Driver">
    </property>
    <property name="url" value="jdbc:jtds:sqlserver://127.0.0.2:1433;databaseName=test">
    </property>
    <property name="username" value="***"></property>
    <property name="password" value="***"></property>
</bean>

<bean id="multipleDataSource" class="MultipleDataSource" >
    <property name="targetDataSources">
        <map key-type="java.lang.String">
            <entry value-ref="dataSource1" key="dataSource1"></entry>
            <entry value-ref="dataSource2" key="dataSource2"></entry>
        </map>
    </property>
    <property name="defaultTargetDataSource" ref="dataSource1">
    </property>
</bean>
```

# Usage

When using our AbstractRoutingDataSource, we first set the context and then perform our operation. We make use of a service layer that takes the context as a parameter and sets it before delegating to data-access code and clearing the context after the call.

```java
DynamicDataSourceHolder.setRouteKey("dataSource2");
// data-access operation
DynamicDataSourceHolder.removeRouteKey();
```

Remember that the context is thread bound especially if data access logic will be spanning multiple data sources and transactions, absence of using the removeRouteKey may lead to logical chaos.

# References

[1] AbstractRoutingDataSource: [https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/jdbc/datasource/lookup/AbstractRoutingDataSource.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/jdbc/datasource/lookup/AbstractRoutingDataSource.html)

[2] AbstractRoutingDataSource Example: [https://howtodoinjava.com/spring-orm/spring-3-2-5-abstractroutingdatasource-example](https://howtodoinjava.com/spring-orm/spring-3-2-5-abstractroutingdatasource-example)

[3] Spring实现动态数据源切换: [http://www.cnblogs.com/davidwang456/p/4318303.html](http://www.cnblogs.com/davidwang456/p/4318303.html)

[4] A Guide to Spring AbstractRoutingDatasource: [https://www.baeldung.com/spring-abstract-routing-data-source](https://www.baeldung.com/spring-abstract-routing-data-source)

[5] 使用AbstractRoutingDataSource实现多数据源切换: [https://www.jianshu.com/p/a042ff2ee2ae](https://www.jianshu.com/p/a042ff2ee2ae)





