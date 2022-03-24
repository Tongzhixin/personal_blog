---
title: "Windows 启动项查看编程"
date: 2021-06-11T15:24:27+08:00
description: "粗糙记录我这学期Windows启动项查看的开发"
tags: 
    - Windows
    - 课程设计
categories:
    - Development
    - Record
draft: false
---

### 前言
原版报告可参考：[https://github.com/Tongzhixin/windows_autorun/blob/main/report.md](https://github.com/Tongzhixin/windows_autorun/blob/main/report.md)
记录付出很大精力的Windows安全原理大作业的开发过程以及自己的收获感悟。


此次大作业要求编写一款查看windows自启动项的软件，主要涉及了注册表、自启动目录、注册表中的services、drivers、known dlls、ActiveX等等。<!--more-->

### 踩坑点

- 字符串转换问题

  - 这个问题从始至终都在遇到，毫无他法，遇到一个解决一个就可以。
  - 里面出现的字符串大致有两种：`char和w_char`。
  - 又有许多不同的包装类型：`LPTSTR、LPCTSTR、LPCWSTR、LPBYTE、TCHAR、BSTR、WCHAR、QString`等等
  - 我目前的解决方法是包装多个转换函数，遇到一个转换一个，在宽字符与单字节字符之间的转换需要用到`WideCharToMultiByte`和`MultiByteToWideChar`两个函数。
  - 我主要使用标准库中的string类型在各种函数之中转换，在后期减少了我很多麻烦

- 程序异常&heap溢出（**内存泄露**）

  - 大概率是内存泄漏，前期刚写作业时，用到了大量的char *指针，很有可能导致了野指针以及释放不及时等问题，令人头疼，第一次体会到指针也不是太好用。
  - 后来几乎全面转向string类型，就没有出现这个情况了。

- 链接库问题

  - 一开始运行的时候，总是报错显示“无法解析的外部符号` __imp_CryptDecodeObject` ”这种问题，被折磨了好久，被同学提醒需要在变异的时候加上一些libs。

  - ```
    LIBS += \
            -ladvapi32 \
            -lkernel32 \
            -luser32 \
            -lgdi32 \
            -lcrypt32 \
            -lwintrust \
            -ltaskschd \
            -lcomsupp \
            -lshlwapi
    ```

- 计划任务读取报错

  - FTH: (4672): *** Fault tolerant heap shim applied to current process. This is usually due to previous crashes. **
  - 后来查到原来是在递归的时候提早把一个指针无意中给释放了，导致了野指针，从而报错，这个也会导致程序异常。

- exe运行报错问题

  - QT直接执行exe时显示无法定位程序输入点`eventFilter@QAbstractItemView@@MEAA_NPEAVQObject@@PEAVQEvent@@@`于动态链接库
  - 原因是exe运行时找不到相应的依赖dll位置，有两种情况会导致其出现
    - 系统中多个编译器版本，且在path中都有定义，这样exe第一个找到的dll不符合其要求
    - 环境变量path中没有添加编译器相应的路径
  - 解决方法也有两种
    - 添加编译器bin路径到环境变量path中，越靠前越好
    - 使用QT自带的`windeployqt`工具对所需的dll进行抽取，这样exe会寻找本地目录下的dll进行加载。

### 收获

- 这是我这学期写的第二个比较复杂的软件，上一个软件使用的是java语言，这次全面使用c++，我上一次使用c++应该还是学习数据结构时，所以写完这个程序后感知最明显的是c++更熟悉了，对标准库中的类型使用更加熟练，对于指针也有了更深入的理解。
- 本次程序编写属于Windows编程，运行在Windows平台上，用到了很多Windows32的API，因此需要阅读大量MSDN上面的官方文档以及相关的博客文章进行API的了解与学习。这些工作使我对Windows自启动项的技术原理、管理使用有更加深入的理解，尤其是对注册表的理解更深了，以前没有接触过注册表。也大致对Windows编程的基本过程更熟悉了。
- 由于需要编写一个图形界面，我使用了`QTWidget`进行实现，由于这次的重点不在图像界面上，我在图形界面中仅使用了一个`TabWiget`作为容器。虽然工作量很少，但是对QT搭建图形界面的基本结构和运行过程有了一定了解。
- 提高了我信息检索与解决问题的能力。在整个作业过程中，我遇到了很多问题，有简单的也有复杂的。以前遇到问题只知道去网上查，现在我能更加熟练的使用断点debug，判断问题出处，然后考虑解决方法，能更精准地解决问题，大大提高了解决问题的效率。

### 问题

- windows COM服务

#### 感想



虽然看上去工作量很少，但实际上需要花费大量的时间进行调试相关工作。

windows编程是真的复杂，而且很多种类型字符串让人无比头疼，需要花费很多时间在整理类型转换上面。最重要的一点是，意识到了指针的复杂性，野指针太可怕了。

希望字符串相关的类型早日实现统一，windows抛弃历史包袱！