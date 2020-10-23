---
layout: post
title:  "Integrating ReactJS with Spring Boot"
date:  2019-08-21 10:19:02
categories: React Spring
permalink: /archivers/Integrating-ReactJS-with-Spring-Boot
---

Xxxxx Introduction Xxxxx xxxxx.

<!--more-->

# Serving Static Web Contents with Spring Boot

Serving static web contents (such as image, css & js) is necessary when developing a web application. Spring Boot provides excellent supports for it. Its basic configurations can satisfy most of needs in developing, and the developers can also make some custom configurations based on it.

## The Default Static Resource Mapping

By default, Spring Boot maps all visits of "/\*\*", to these directories:

```
classpath:/static
classpath:/public
classpath:/resources
classpath:/META-INF/resources
```

Let's perform a little experiment. Create three directories *static*, *public* and *resources* under *main/resources*, then put *a.png*, *b.png* and *c.png* into *static*, *public* and *resources* respectively. Run the project, and visit:

```
http://localhost:8080/a.png
http://localhost:8080/b.png
http://localhost:8080/c.png
```

All images can be visited, which means Spring Boot looks for static resources in these directories by default.

By tracing the code we found that the default mapping was defined in **WebMvcAutoConfiguration**:

```java
public void addResourceHandlers(ResourceHandlerRegistry registry) {
    if (!this.resourceProperties.isAddMappings()) {
        logger.debug("Default resource handling disabled");
    } else {
        Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
        CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
        if (!registry.hasMappingForPattern("/webjars/**")) {
            this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
        }
        // here is the default mapping defination
        String staticPathPattern = this.mvcProperties.getStaticPathPattern();
        if (!registry.hasMappingForPattern(staticPathPattern)) {
            this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
        }

    }
}
```

The **staticPathPattern** was defined in **WebMvcProperties**, its default value is "/\*\*"; And the  **staticLocations** was defined in **ResourceProperties**, its default value are: "classpath:/META-INF/resources/", "classpath:/resources/", "classpath:/static/", "classpath:/public/", which assigned by variable **CLASSPATH_RESOURCE_LOCATIONS**.

## Customize the Mappings

We can also customize the mapping by implementing **WebMvcConfigurer**.

```java
@Configuration
public class MyWebMvcConfig implements WebMvcConfigurer {
    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        // map "/static/**" to "classpath:/mystatic/"
        registry.addResourceHandler("/static/**").addResourceLocations("classpath:/mystatic/");
    }
}
```

Create new directory *mystatic* under *main/resources*, and put *d.png* in it. Run the project and visit:

```
http://localhost:8080/static/d.png
```

The image *d.png* can be visited in this url.

We can also config the mappings in ***application.properties***:

```
# config the static resource path prefix, override default
spring.mvc.static-path-pattern=/mystatic/**
# config the static resource directories, override default
spring.resources.static-locations[0]=classpath:/mystatic
spring.resources.static-locations[1]=classpath:/public
```

Restart the project, the path below can be visited:

```
http://localhost:8080/mystatic/a.png
```

And this path can't be visited:

```
http://localhost:8080/a.png  // default prefix has been overridden
```

Resources under *mystatic* and *public* can be visited; But when visiting *resources* and *static*, a "404 not found" message will be returned, because this configuration overrides the default mappings.

# Officially Supported Way to Create React App

First of all, we need to setup the developing environment for our laptop. What we need to install is the **npm** (Node Package Manager). Run the command below:

```
brew install node    # for macOS
```

After installation, run:

```
node -v
npm -v
```

If we get the versions of **node** and **npm**, the installation was successful.

Second, we need to install **create-react-app** globally via **npm**:

```
npm install -g create-react-app
```

The **create-react-app** is an officially supported way to create single-page React applications. It offers a modern build setup with no configuration.

Now, let's start building a react project called "react-tutorial-codes":

```
create-react-app react-tutorial-codes
```

After a few minutes, a new directory "react-tutorial-codes" was generated, the structure in it is:

```
node_modules        # folder, tools and dependencies
public              # folder, index.html and root elements
src                 # folder, javascript files
.gitignore
package.json        # information about project and outer dependencies
package-lock.json
README.md
```

Enter this directory and run this demo project:

```
cd react-tutorial-codes
npm start
```

Open your web browser, and goto: **localhost:3000/**, a react demo page will be presented.

# Integrating React with Spring Boot

Xxxxx

# Deploy Separately & CORS Problem

It's a common problem when you are deploying your frontend app that the backend api is hosted on a different origin.

Xxxxx.

# References

[1] Spring Boot 静态资源访问和配置全解析: [https://blog.csdn.net/u010358168/article/details/81205116](https://blog.csdn.net/u010358168/article/details/81205116)

[2] React Getting Started: [https://www.w3schools.com/react/react_getstarted.asp](https://www.w3schools.com/react/react_getstarted.asp)

[3] Tutorial - Intro to React: [https://reactjs.org/tutorial/tutorial.html](https://reactjs.org/tutorial/tutorial.html)

[4] Create React App Docs: [https://create-react-app.dev/docs/getting-started](https://create-react-app.dev/docs/getting-started)

[5] Xxxx Xxxx: []()

[6] Xxxx Xxxx: []()

[7] Xxxx Xxxx: []()

[8] Xxxx Xxxx: []()

[9] Xxxx Xxxx: []()

