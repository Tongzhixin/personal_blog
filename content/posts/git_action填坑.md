---
title: "Github Actions 使用踩坑 "
date: 2021-05-17T15:24:27+08:00
description: "计算机病毒大作业中部分的工作"
tags: 
    - 工具使用
categories:
    - Record
draft: false
---

主要是environment secret与repo的secret是不同的，在step中引用的是repo的secret，在env中引用的是env的secret。
<!--more-->

https://blog.benoitblanchon.fr/github-action-run-ssh-commands/

https://docs.github.com/cn/actions/reference/environment-variables

![image-20210517125008591](/assets/git_action%E5%A1%AB%E5%9D%91/image-20210517125008591.png)