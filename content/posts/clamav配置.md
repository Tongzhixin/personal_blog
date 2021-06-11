---
title: "clamAV配置"
date: 2021-04-22T15:24:27+08:00
description: "计算机病毒大作业中部分的工作"
tags: 
    - 工具使用
    - 课程设计
categories:
    - Record
draft: false
---


## ClamAV编译安装与检测原理

ClamAV在部分发行版本中，可以直接使用官方维护的包管理软件进行安装，但是为了更好的使用体验、契合宿主机操作系统运行以及自定义安装等需求，在此项目中，小组进行了源码的自定义编译与安装。
<!--more-->

### ClamAV介绍

Clam AntiVirus（ClamAV）是免费而且开放源代码的杀毒软件，软件与病毒码的更新皆由社区免费发布。目前ClamAV主要是使用在由Linux、FreeBSD等Unix-like系统架设的邮件服务器上，提供电子邮件的病毒扫描服务。ClamAV本身是在终端下运作，但也有许多图形接口的前端工具（GUI front-end）可用，另外由于其开放源代码的特性，在Windows与Mac OS X平台都有其移植版。 

#### 特性：

- 能够用于快速扫描文件
- 可以进行实时检测（仅限于Linux），以守护进程的形式运行在后台
- ClamAV应用领域广泛，尤其在电子邮件反病毒领域，在多个平台检测出超过10万条病毒、蠕虫、木马等恶意程序，侦测率较高
- 病毒库能够做到及时更新，至少每四个小时更新一次
- 内置的解释器允许用户进一步开发和扩展，具有很好的拓展性
- ClamAV可以扫描多种类型的文件
  - 目前可以支持Zip，RAR，Tar，Gzip，Bzip2，OLE2，Cabinet，CHM，BinHex，SIS格式、大部分的邮件格式、可执行的可链接格式（ELF）、以UPX，FSG，Petite，NsPack，wwpack32，MEW，Upack压缩和以SUE，Y0da Cryptor混淆（obfuscate）的可移植的可执行文件（PE）
  - 它亦支持许多文档的格式，包括：Microsoft Office、HTML、RTF和PDF

### 编译安装

在此次编译安装过程中，我们主要参考了刘功申老师实验平台上的使用[教程](http://202.120.39.107:7709)以及[ClamAV官方网站](https://www.clamav.net/documents/installation-on-debian-and-ubuntu-linux-distributions)的指导手册。

#### 安装过程

1. 安装虚拟机，下载优麒麟最新版本的镜像文件，在虚拟机中安装优麒麟

   ![image-20210422234058632](/assets/clamav%E9%85%8D%E7%BD%AE/image-20210422234058632.png)

2. 在官网或者GitHub等地址下载ClamAV程序源码

   ![image-20210422234151132](/assets/clamav%E9%85%8D%E7%BD%AE/image-20210422234151132.png)

   

3. 按照官网的指导手册，安装相关的依赖包

   ![image-20210422234244932](/assets/clamav%E9%85%8D%E7%BD%AE/image-20210422234244932.png)

   ```sh
   sudo apt-get install build-essential
   sudo apt-get install openssl libssl-dev libcurl4-openssl-dev zlib1g-dev libpng-dev libxml2-dev libjson-c-dev libbz2-dev libpcre2-dev ncurses-dev
   sudo apt-get install valgrind check check-devel
   ```

   

4. 按照不同的自定义安装方式，添加相应用户，并赋予权限，这一步骤可以按照老师实验平台的教程

   ```sh
   sudo groupadd clamav
   sudo useradd -g clamav -s /bin/false -c "Clam AntiVirus" clamav
   sudo chmod -R 777 clamav
   ```

5. 进行相关的配置以及手动编译安装

   ```sh
   cd clamav
   ./configure --prefix=/home/sjtu/clamav  --disable-clamav
   sudo make
   sudo make install
   ```

6. 按照指导手册要求，更改部分文件的权限，以及样例配置文件，运行测试

   ```sh
   sudo mkdir ~/clamav/share/clamav
   sudo chmod 777 ~/clamav/share/clamav/
   cd ~/clamav/etc
   cp clamd.conf.sample clamd.conf
   cp freshclam.conf.sample freshclam.conf
   #修改样例配置文件，编辑每个文件并删除或注释掉Example行
   #进行测试扫描
   cd ~/clamav/bin
   ./freshclam
   ./clamscan
   ```

7. 安装完成

#### 问题&解决方案

- 依赖问题: `error: openssl misconfigured or missing`
  - 原因是缺少基础组件 
  - 前往官网查看指导手册，安装所有必要依赖
- 报错：`error: The database directory must be writable for UID 1001 or GID 1001`
  - 原因是未能更改共享文件夹的所属用户
  - `chown  clamav:clamav  /usr/local/share/clamav/`即可

### 检测原理

#### 检测步骤说明

1. 当收到扫描文件的命令后，ClamAV初始化配置信息，并加载病毒库
2. 将病毒库进行解压缩处理，如果解压失败，直接退出；
3. 解压后的文件根据后缀和文件格式的不同被引擎利用不同方式转化为引擎可识别的结构信息，便于之后的多模式匹配过程
4. ClamAV扫描待识别文件，如果是压缩包，则进行解压缩处理，循环至解压出的文件入口处
5. 通过文件类型识别检测，针对不同的文件格式采集不同的信息作为模式匹配的一方
6. 其中如果是MSEXE格式，需做特殊化处理：进行多态形病毒检测，判断是否进行了加壳处理，如是，则进行脱壳处理，返回至初始处
7. 对病毒库信息与扫描文件信息两方使用AC/BM多模式匹配算法进行处理，得出扫描结果

检测原理图如下图所示：

![image-20210422231437035](/assets/clamav%E9%85%8D%E7%BD%AE/image-20210422231437035.png)

#### AC/BM算法

AC/BM算法是在AC自动机的基础之上，引入了BM算法的多模扩展，实现的高效的多模匹配。和AC自动机不同的是，AC/BM算法不需要扫描目标文本串中的每一个字符，可以利用本次匹配不成功的信息，跳过尽可能多的字符，实现高效匹配。AC/BM算法的核心思想是让每次匹配的起始位置跨度尽可能的大，以提高效率。

AC/BM算法中的AC算法部分比AC自动机算法的实现要简单，不需要考虑失效函数的问题，也就是说ACBM算法中实现的AC算法部分是一棵树，而在AC自动机的实现是一个图。AC/BM算法中的BM算法的实现要比BM算法本身的实现要复杂一些，因为这是对BM算法的多模式一种扩展。

AC/BM算法中的核心数据结构：

- MinLen，模式串集合中最短那个模式串的长度：比较失配时最多跳跃的字符个数不能超过Minlen。
- ACTree，由模式串集合构建出的状态树，构建方法和AC自动机的构建方法相同，而且不需要计算失效函数，较为简单。
- BCshift[256]：ACTree对应一个坏字符数组，当匹配失效时，查找该数组计算坏字符偏移量。
- GSshift：AC树的每一个节点对应一个好后缀偏移量。

参考文章：

- https://blog.csdn.net/sealyao/article/details/4560427
- https://blog.csdn.net/sealyao/article/details/6817944





