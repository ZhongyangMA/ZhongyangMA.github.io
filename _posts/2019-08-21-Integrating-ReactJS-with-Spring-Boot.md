---
layout: post
title:  "Integrating ReactJS with Spring Boot"
date:  2019-08-21 10:19:02
categories: React Spring
permalink: /archivers/Integrating-ReactJS-with-Spring-Boot
---

Nowadays, architectures with separate frontend & backend deployments are becoming more and more popular. Because it decouples the frontend from the backend, improves maintainability of the code and the work efficiency. In this article, by tracing the source code, I introduce how the static web contents are hosted in Spring Boot; Then I present the common ways to create a React application; In the end, I illustrate the methods of hosting frontend & backend on different origins, and discuss the problems relating to CORS policy. This is an easy-to-follow reading, suitable for full stack learners.

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

Open your web browser, and goto: **localhost:3000/**, an official react demo page will be presented.

# Integrating React with Spring Boot

Please note that the development build above is not optimized, to create a production build, run:

```
npm run build
```

Before we run this command, we need to change the homepage location to the current directory. Open ***package.json***, and add `"homepage": "."` field to the end of it:

```json
{
  "name": "react-tutorial-codes",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@testing-library/jest-dom": "^4.2.4",
    "@testing-library/react": "^9.5.0",
    "@testing-library/user-event": "^7.2.1",
    "react": "^16.13.1",
    "react-dom": "^16.13.1",
    "react-scripts": "3.4.3"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": "react-app"
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  },
  "homepage": "."
}
```

Then by running `npm run build`, a new folder "build" is generated in this project. Within it, the "index.html" is the entry of this react project. 

To deploy this react project to a Spring Boot server, copy all things from "build" to Spring Boot's "main/resources/public" folder. Then start Spring Boot project, and visit **localhost:8080/**, the same official react demo page will appear.

## The Sample Codes

**react-tutorial-codes**: [https://github.com/ZhongyangMA/react-tutorial-codes](https://github.com/ZhongyangMA/react-tutorial-codes) This sample code requests backend API via "axios". Run `npm run build`, then copy all contents from "build" folder to "main/resources/public" folder in the Spring Boot project below.

**react-deployment-samples**: [https://github.com/ZhongyangMA/react-deployment-samples](https://github.com/ZhongyangMA/react-deployment-samples) This is a Spring Boot project which provides several backend APIs for the React frontend. Start the project and visit **localhost:8080/** to get frontend contents to your browser, then click the "Call Backend API" button in this demo page to request backend API **localhost:8080/api/users**.

# Deploy Separately & CORS Problem

The React project can be deployed separately from backend servers. It can be hosted on a nginx server for instance.

Run commands below to install and setup nginx server:

```
brew install nginx      # for macOS
cd /usr/local/etc/nginx
vi nginx.conf           # set ip and port, for instance localhost:8088
```

Then copy all contents from React "build" folder to nginx's "/usr/local/var/www" folder, and start nginx server:

```
nginx
```

Visit **localhost:8088** in your web browser, the same official react demo page will appear again.

But if you click the button "Call Backend API" now, you will get error message "Access to XMLHttpRequest at 'http://localhost:8080/api/users' from origin 'http://localhost:8088' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource".

**CORS**, Cross-Origin Resource Sharing, is a common problem when you are deploying your frontend app that the backend api is hosted on a different origin (different *protocol*, *ip* or *port*). It is a browser mechanism which enables controlled access to resources located outside of a given domain.

To add "Access-Control-Allow-Origin" header on the requested resource, we can simply put **@CrossOrigin** annotation before the Restful API in Spring Boot.

```java
@RestController
@RequestMapping(value = "/api")
public class ApiController {
    // origins, whitelist of allowed origins, "*" means allow all
    // maxAge, age of cookie in second
    @CrossOrigin(origins = "*", maxAge = 3600)
    @RequestMapping(method = RequestMethod.GET, value = "/users")
    public List<User> getUsers() {
        // code omitted
        return list;
    }
}
```

The **@CrossOrigin** annotation can be added before methods and classes, you can specify a list of allowed origins, or use "\*" symbol to allow all the cross-origin requests.

# References

[1] Spring Boot 静态资源访问和配置全解析: [https://blog.csdn.net/u010358168/article/details/81205116](https://blog.csdn.net/u010358168/article/details/81205116)

[2] React Getting Started: [https://www.w3schools.com/react/react_getstarted.asp](https://www.w3schools.com/react/react_getstarted.asp)

[3] Tutorial - Intro to React: [https://reactjs.org/tutorial/tutorial.html](https://reactjs.org/tutorial/tutorial.html)

[4] Create React App Docs: [https://create-react-app.dev/docs/getting-started](https://create-react-app.dev/docs/getting-started)

[5] Cross-origin resource sharing: [https://portswigger.net/web-security/cors](https://portswigger.net/web-security/cors)

[6] CORS with Spring: [https://www.baeldung.com/spring-cors](https://www.baeldung.com/spring-cors)

