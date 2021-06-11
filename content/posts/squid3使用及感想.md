---
title: "Squid3工具使用"
date: 2020-02-01T15:24:27+08:00
description: "在使用这个工具做http代理时遇到了很多问题，貌似当时也没有解决，记录以便后来观看"
tags: 
    - 工具使用
categories:
    - Record
draft: false
---

#### 利用squid3做成https代理

昨天做了很多功课，记录一下失败经验和过程，以便如果有时间再进行折腾，现在已经折腾不下去了。

首先由于squid官方源 sb，并未集成ssl模块，需要自己编译，这是源头。

然后`apt-get install openssl libssl-dev ssl-cert`,这个过程还算正常，然后下载源码：

```
apt-get source squid
apt-get build-dep squid
apt-get install devscripts build-essential fakeroot
```

在这个过程中，我出现一些毛病，就是关于用户_apt的问题，这里推荐一个[链接](https://askubuntu.com/questions/908800/what-does-this-synaptic-error-message-mean),比较容易解决，但是要注意权限的发布与控制。

之后就是万恶的修改配置

```
cd squid3-3.5.12
vi debian/rules
```

，然后我看了很多博客，也知道了要添加

```
--with-openssl \
--enable-ssl-crtd \
```

有的一些博客是`--with-open-ssl`这是比较老的，还有一些博客是需要添加这两个

```
--enable-ssl 
--enable-http-violations#高匿代理
```

我试了很多次，应该要有这四个或者前三个，不过总是编译不通过，但是只要去掉了`--with-openssl`就好了，wsl。

然后就是安装 包：

```
dpkg -i squid3-common_3.4.8-6+deb8u4_all.deb  squid3_3.4.8-6+deb8u4_amd64.debsquid3-dbg_3.4.8-6+deb8u4_amd64.deb
```

按需，如果是amd就安装amd，如果是其它，就安装其它。

安装之后就要对squid进行配置，这里推荐[这位]([https://telanx.github.io/2017/09/27/Squid%E5%88%87%E6%8D%A2%E5%88%B0https%E4%BB%A3%E7%90%86/](https://telanx.github.io/2017/09/27/Squid切换到https代理/))配置

![squid1](/assets/squid3%E4%BD%BF%E7%94%A8%E5%8F%8A%E6%84%9F%E6%83%B3/squid1.png)

配了好长时间仍然失败，wsl。

然后我又转换成tinyproxy就可以了，这个非常简单，wsl！



-----

>  更新自2020年1月23号

#### tinyproxy使用

首先我们要明确一个概念，代理服务器跟用来fq的服务器比如vps有一点是不一样的。你跟代理服务器之间通信是可以被截取的，而一般fq用的，在你主机与服务器之间会有加密。

tinyproxy就是一款轻量型的http代理服务器，用来爬虫还好。

`apt-get install tinyproxy`

`service tinyproxy start/restart/stop`

`/etc/tinyproxy.conf`

- 端口要改
- Allow要改
- 密码，随便吧



