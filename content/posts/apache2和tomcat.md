---
title: "了解apache2与tomcat"
date: 2020-02-01T19:24:27+08:00
description: "在整活的时候碰到了两个陌生词，所以进行了了解和使用，本文是折腾的一些记录"
tags: 
    - 自我学习
    - 工具使用
categories:
    - Development
    - Record
draft: false
---

### apache2和SSL设置（不含tomcat安装方法）

#### 前言（更新自2020-8-17，将以前的两者合并）

现在回顾：有点幼稚

### 正文

也叫httpd.service ；在与tomcat结合的过程中需要利用到mod_jk2连接器，但是利用apt 安装的方法不自带apxs，所以需安装apache2-dev，编译的过程，推荐[这篇博客](https://www.jianshu.com/p/b62aca3c516b)。然后就到了配置文件了

最后将tomcat-connectors-1.2.41-src/conf下的三个配置文件复制到/usr/local/apache2/conf/下，并将其中的httpd-jk.conf改名为mod-jk.conf.

配置关于apache2的一些变量，在这之前也要注意一些环境配置——/etc/environment

之后的各种conf推荐[这个大神](https://blog.csdn.net/u011726984/article/details/51226662)。`CATALINA_HOME=~/develop/tomcat7`这是这个环境格式的写法。

我仅推荐这两篇博客，至于安装tomcat和java环境，自行Google，而安装位置，建议[这篇](https://blog.csdn.net/aqxin/article/details/48324377)。

推荐更高级的tomcat和java环境。

`update-alternatives --config `：检查java环境，很有用。

<!--more-->

### 补充

#### apache2 ssl证书安装



- ##### 申请证书阶段

  难受的一批。。

  首先我们先来了解https，就是在http的基础上把通信进行stl加密，加密的方式就是通过证书的方式以及不同方式的加解密。此处推荐观看[菜鸟家]('https://www.runoob.com/w3cnote/https-ssl-intro.html')家的简介，主要介绍的是https通信中的过程。

  然后我来说证书，又叫做ca数字证书。你通过csr文件向ca数字机构申请服务证书，这个服务证书又被称为公钥，就是你可以给别人的，都可以看的。同时你申请的机构还会给你一些ca数字证书文件，又叫做中间文件，布置在浏览器中的一些根证书可以信任这些中间数字证书，这也是https的特点。这样一来就可以保证是拥有数字证书的人在与根证书通信。

  所以简单来说，你通过csr向ca机构（也就是收钱的厂家）申请到两个文件：服务器证书（公钥）、ca数字证书（被信任中间商的名片）。然后就可以安装了。

- ##### 安装apache2及配置

  这是我折腾最久的地方，原因很羞耻：我忘记了软链接是需要绝对路径的，

  1. 证书准备

     上面提到的服务器证书、中间商ca名片、密钥（在生成csr文件时获得）

  2. apache2安装

     自己看网上教程。提醒观察它的配置文件结构，以及avaliable和enabled之间的关系。

  3. 添加SSL支持

     首先先给apache添加SSL支持模块。Apache2的做法是将`mods-available`中的`ssl.conf`,`ssl.load`,`socache_shmcb.load`三个文件放到`mods-enabled`中

     提醒：命令`apachectl configtest`能查看配置文件是否正确 ,以确保Apache不会死掉

  4. 创建sites-avaliable/default-ssl的软连接到sites-enabled并修改它成为：

     ```
     <VirtualHost *:443>  //修改端口
     SSLEngine on  //开启引擎
     SSLCertificateFile //服务器证书（公钥）绝对路径  //上面证书存放的位置
     #如果是第三方发放的证书，会有证书私钥文件、证书公钥文件、证书链文件需要配置
     SSLCertificateKeyFile xxxxx//私钥绝对路径
     SSLCertificateChainFile  xxxxx//中间商名片
     ```

     然后

     ```undefined
     service apache2 reload
     apachectl reload
     ```

     重启

     `service apache2 restart`

- ##### 实现http自动跳转https

  打开 `/etc/apache2/sites-available/000-default.conf`，
  在`<VirtualHost *:80></VirtualHost>`标签内随便一个地方加入以下三行:

  ```csharp
  RewriteEngine on
  RewriteCond  %{HTTPS} !=on
  RewriteRule  ^(.*) https://%{SERVER_NAME}$1 [L,R]
  ```

再重启就好了

这样就可以配好了。。

-------- ------------ ----------------

#### 长话短说

​		命令：a2enmod ssl和a2dismod是开启和关闭模块命令

服务器证书可以使用certbot这个[生成](https://certbot.eff.org/),域名自己申请一个就可以了。

并且我仍然记得上次我曾详细的写了一次，不知道怎么滴被删了。。。。

v2ray使用tls，但是不知道能否使其与apache2共存。。。
