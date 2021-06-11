---
title: "php探索时的一些配置"
date: 2019-07-23T15:24:27+08:00
description: "现在我也想不起来当时为什么要弄这个东西，php难道还没有过时吗？本人了解的很少，但是在信息综合实践课上使用过一些语法，感觉这个语言很不好用"
tags: 
    - 编程语言
    - PHP
categories:
    - Development
    - Record
draft: false
---

### 目的

我最近买了一个服务器，阿里的最便宜的学生优惠：ecs。作为一个服务器，我觉得应该搞一搞web服务，我就开始找各种资料，网上充斥了各种资料，有应用型的比如javajdk、tomcat、mysql还有nodejs搭建web服务器可以参考[博客](https://www.jianshu.com/p/a05835de5853 "nodejs搭建服务器")，另外还有lamp、wamp，等等，因为我想了解了解php语言，所以我选择了lamp搭建环境。

<!-- more -->

### 推荐[博客](https://blog.csdn.net/sdkdlwk/article/details/79839962 "搭建Ubuntu lamp")

在看这一类内容时，你会发现其实什么操作系统安装都是没有问题的，关键是你要了解这个web服务器运行的一些原理，比如你为什么输入ip地址最后就能显示你的apache静态网页，等等。

### 安装时遇到的一些问题及解决方案

安装apache2、myaql都是没有问题的在安装php时就有问题了
网上一般教程直接`apt-get install phpx.x`，但是网上现在稳定版只至php7.0或者php7.1所以就要用ppa(personal package archive),进行安装，具体概念可参考[这个网页](https://itsfoss.com/ppa-guide/ "ppa 指导")底下是相关命令：

```
 #apt-get install software-properties-common（$apt-get install python-software-properties（事先安装）
$ add-apt-repository ppa:ondrej/php
$ apt-get update
$ apt-get upgrade php
```

然后再进行安装时就安装其他博客上指出的一些相关拓展即可
