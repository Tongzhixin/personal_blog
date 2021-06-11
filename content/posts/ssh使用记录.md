---
title: "ssh相关记录"
date: 2019-07-17T15:24:27+08:00
description: "希望能够持续记录在开发过程中遇到的ssh问题，目前只有一次2019年记录的踩坑，其实目前（2021年6月）看来已经不算坑了，希望之后能够持续记录"
tags: 
    - SSH
    - 踩坑记录
categories:
    - Development
    - Record
    - 解决问题
draft: false
---



### ssh 远程拒绝

> 2020年2月更新

##### ssh问题示例：

![image-20200202123452465](/assets/ssh%E4%BD%BF%E7%94%A8%E8%AE%B0%E5%BD%95/image-20200202123452465.png)

当我遇到这个问题时，我又深入的想了一下（算是深入叭）：

在~/.ssh文件夹中，有两个文件需要注意：config和known_host，一个是配置，后一个是第一次连接之后就保存的一个东西，以便之后连接的时候就不会出现，就可以直接连接了。但是我这个想法错误了。我一开始的配置中config有一个路径写错了（写成linux中的路径格式），但是known_host是对的，但是这样连接不上，说明了，每次ssh连接的时候还是会使用config配置，而known_host只是充当了认证文件，就像网上说的那样，只是为了认证通信的公钥是否与原本存留的公钥一致，如果不一致，取消交易。所以我猜测，如果你将公钥换去的第一次连接肯定会有提示，并重写一下known_host文件。

<!-- more -->

这是config格式：记得tab空出来

```
Host github.com
	User git
	Hostname ssh.github.com
	PreferredAuthentications publickey
	IdentityFile ~/.ssh/id_rsa   #如果是Windows需要改写路径格式哦
	Port 443

Host gitlab.com
	Hostname altssh.gitlab.com
	User git
	Port 443
	PreferredAuthentications publickey
	IdentityFile ~/.ssh/id_rsa
```

-------

新增加的一点注意项：

- 注意config的开头字母的大写
- 如果总是失败，试试换一下Host的名称，或许会有意外收获哦！
- 常用调试：`ssh -T git@github.com`、`ssh -T -p 443 git@ssh.github.com`、`ssh -T git@github.com`等等
----

>  2019年记录

----

### vscode远程控制的准备

#### 简介：刚开始使用ssh密钥时的踩坑记录

 在网站上下载[openssh/（win10/）](https://www.mls-software.com/opensshd.html,"下载来源")，至于与putty和git生成的密钥，网上一致的声音是，openssh暂时没有后两者好用，但是当我把坑踩了好多之后，明白了ssh的道理之后就感觉几种之间并没有太大的区别，因为都是用的ssh机制，只不过是一种验证工具。下面是我总结的一点语句：
 首先我们要清楚ssh密钥和配置都在`C:\Users\名字\.ssh`中，名字有几种，分别为`id_rsa`等`ssh-keygen -t rsa -C who@where、scp`，我看到一篇[博客](https://qidawu.github.io/2016/10/06/ssh/)，很实用，另外一个使[vscode连接远程操控的方式](https://blog.csdn.net/lenfranky/article/details/89972889) ，另外[这个](https://blog.csdn.net/maguanzhan7939/article/details/94398745)也可以作为参考。

