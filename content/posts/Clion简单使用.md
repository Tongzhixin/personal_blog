---
title: "Clion集成开发工具使用"
date: 2021-03-26T15:23:27+08:00
description: "虽然很早就由同学推荐而转向了Clion，但是一直没有记录相关的使用，这次加以记录"
tags: 
    - 工具使用
categories:
    - Development
draft: false
---

### 需求

今天我在本地IDE写代码的时候，觉得需要在一个Project练习代码，而不是每练习一道题就开一个project，那样太浪费时间了，而且clion这么好用的工具肯定帮我想好的所有的需求。但是我去网上找的时候，可能这个问题比较少，或者大家都知道，所以并没有找到比较好用的资料。
<!--more-->
### 解决

1. 我在Project中建立一个文件夹和一个main.cpp，IDE提醒我要不要将这个东西add_to targets，我随便选择了一个（如图），然后粘贴代码后显示一个tip，然而编译的时候报错：两个main.cpp。然后我想会不会是每个文件下都整一个cmake文件，这样就可以编译通过了？我开始尝试将cmakelists文件复制到刚刚建立的文件夹下面，然而按照提示重构后，并不能变成只能编译这一个项目，其他的文件夹下面的main函数都不能编译了，提示不从属于任何project。。。。![image-20210320173707145](/assets/Clion%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8/image-20210320173707145.png)
2. 最终方案：这个时候我意识到cmakelists应该只能存在一个，新建了一个项目，新建立文件夹和源文件，在提示是否加入target的时候，加入即可，然后修改project下面的cmakelists.txt，添加一行`add_executable(test_dir "test_dir/main.cpp")`就可以了。这个时候运行相应的文件夹下的main函数，而且不会跟vscode一样要一下子全编译。