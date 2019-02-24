---
layout: post
title:  "Dynamic Data Source Routing in Spring"
date:  2019-02-17 12:24:41
categories: Java Spring
permalink: /archivers/Dynamic-Data-Source-Routing-in-Spring
---

Recently we designed a system that needs to switch the data sources based on the region infos in each request. We accomplished that idea by adopting the AbstractRoutingDataSource, which is a very useful feature in spring framework if you want to choose a particular database when user belongs to certain locale and switch to another locale if user belongs to another locale. In this post, I will show an example that using AbstractRoutingDataSource with a thread-bounded context to implement the dynamic data source routing.

<!--more-->

# Title 1

## Title 2

xxxxxxxxxxxx

# References

[1] AbstractRoutingDataSource: [https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/jdbc/datasource/lookup/AbstractRoutingDataSource.html](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/jdbc/datasource/lookup/AbstractRoutingDataSource.html)

[2] AbstractRoutingDataSource Example: [https://howtodoinjava.com/spring-orm/spring-3-2-5-abstractroutingdatasource-example](https://howtodoinjava.com/spring-orm/spring-3-2-5-abstractroutingdatasource-example)

[3] Spring实现动态数据源切换: [http://www.cnblogs.com/davidwang456/p/4318303.html](http://www.cnblogs.com/davidwang456/p/4318303.html)

[4] A Guide to Spring AbstractRoutingDatasource: [https://www.baeldung.com/spring-abstract-routing-data-source](https://www.baeldung.com/spring-abstract-routing-data-source)

[5] 使用AbstractRoutingDataSource实现多数据源切换: [https://www.jianshu.com/p/a042ff2ee2ae](https://www.jianshu.com/p/a042ff2ee2ae)





