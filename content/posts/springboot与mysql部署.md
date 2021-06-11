---
title: "springboot docker部署"
date: 2021-04-21T15:24:27+08:00
description: "应用软件设计中需要将后端开发的部分二进制jar包部署在云服务器上"
tags: 
    - 工具使用
    - 课程设计
    - docker
categories:
    - Record
    - Development
draft: false
---

### springboot与mysql部署

#### 思路

因为maven是可以将springboot的所有代码进行打包，并且由于内置tomcat，可以直接在jdk下运行，所以主要思路就是使用docker将mysql以及java的环境进行部署。
<!--more-->

#### 过程

先稍微复习docker的使用、dockerfile的写法、docker-compose使用；

参考链接：https://yeasy.gitbook.io/docker_practice/compose/compose_file

docker-compose可以将不同的服务同时部署，非常方便，这里列出官方给出的写法：

```
version: '3.1'
services:
  backendjava:
    build:
      context: ./Dockerfiledir
      dockerfile: Dockerfile
    ports:
     - 80:8000
    depends_on:
     - mysqldb
  mysqldb:
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: xxx
      MYSQL_USER: test
      MYSQL_PASSWORD: xxx
    ports:
     - 3306:3306
```

需要把spring部署到服务器上，但是在本地打包的过程中，会出现error：[Error creating bean with name 'entityManagerFactory' defined in class path resource : Invocation of init method failed](https://stackoverflow.com/questions/40058001/error-creating-bean-with-name-entitymanagerfactory-defined-in-class-path-resou) 之类的问题，这时候需要加入这两行，其中第一行的MYSQL5，5特别重要，处理不好会影响后面过程。

另外，第二行主要解决上面自动创建数据库表的问题，并且有很多种方式以及解决方案，如下图所示：

```
spring.jpa.database-platfom=org.hibernate.dialect.MySQL5Dialect
spring.jpa.properties.hibernate.hbm2ddl.auto=update     
```

![QQ图片20210421230056](/assets/springboot%E4%B8%8Emysql%E9%83%A8%E7%BD%B2/QQ%E5%9B%BE%E7%89%8720210421230056.png)

最后记得打开相应端口，就可以正常运行。