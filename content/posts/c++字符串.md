---
title: "C++字符串的一些整理"
date: 2021-03-26T15:24:27+08:00
description: "C++的字符是一个大坑，一旦遇到，最好进行记录，不知道啥时候可能就用上了"
tags: 
    - C++
    - 编程语言
categories:
    - Development
    - Record
    - 编程语言
draft: false
---

引言

> 由于在牛客网上的题目都需要自己定义输入输出，我需要总结一下，目前我得到的知识和经验

<!--more-->

#### 转换之类

- 数字转换为字符串

  - to_string()

  - sstream类也可以达到这样的效果，需要记住的是他并没有输入流。

    - ```
      ostringstream stream;
      stream<<n;
      return stream.str();
      ```

  - char数组以及char*与string的转换 注：这篇文章很详细（https://blog.csdn.net/hebbely/article/details/79577880、https://blog.csdn.net/ksws0292756/article/details/79432329）

    - string->char[]：strnpy（char[],string.c_str()）
    - string->char *直接赋值即可

- 字符串转换为数字 https://blog.csdn.net/candadition/article/details/7342380

  - stoi、stol、stoll、atof、atoi、atil……
  - ANSI C 规范定义了 [stof()](http://c.biancheng.net/cpp/html/124.html)、[atoi()](http://c.biancheng.net/cpp/html/125.html)、[atol()](http://c.biancheng.net/cpp/html/126.html)、[strtod()](http://c.biancheng.net/cpp/html/128.html)、[strtol()](http://c.biancheng.net/cpp/html/129.html)、[strtoul()](http://c.biancheng.net/cpp/html/130.html) 共6个可以将字符串转换为数字的函数，大家可以对比学习。另外在 C99 / C++11 规范中又新增了5个函数，分别是 atoll()、strtof()、strtold()、strtoll()、strtoull()
  - 其中strtod需要stdlib.h并且需要strtod（b，NULL）表示
  - stof方便，atof针对char字符串
  - 待补充

- 字符串输入

  - cin>>中间不能有空格
  - cin.get()可作为清空缓冲区，中间的参数为数字
  - cin.getline(a,20,'')一行字符串
  - getline(cin,str)针对string

- sstream 

  - 截断字符串
  - 转换格式
  - 保存字符串，可持续添加

- bitset



> 由于另一篇文章于此篇有高度重合，我就也复制粘贴过来了

-----

### 背景:

最近编译原理布置作业，进行词法分析，在使用flex工具进行规则处理的时候遇到了需要将16进制字符串转化为10进制字符串的问题。由于很长时间没有接触过c++，很是生疏，而且当初进行学习的时候，并没有学习地很深入，所以在查了一些资料后，我写下这篇文章。

#### 方法：

##### 在这里大致有两种方式，将16进制转为10进制（排除c语言的写法）

- 第一种：

  ```c++
  #include <sstream>
   
  int x;
  stringstream ss;
  ss << std::hex << "1A";  //std::oct（八进制）、std::dec（十进制）
  ss >> x;
  cout << x<<endl;
  输出：26
  ```

- 第二种：

  ```c++
  string out = "1A";
  int x = stoi(out, nullptr, 16);
  cout << x <<endl;
   
  输出：26
  ```

博客内容来源：https://blog.csdn.net/MOU_IT/article/details/89060249，里面很是详细。

#### 其它

另外，由于我对string了解不多，发现其有很多用法。比如std::to_string()的很多用法，以及string和char*的转换，都是很重要的内容，以及string::data()和string::c_str()这些函数的用法，也很重要。

- https://blog.csdn.net/puppylpg/article/details/51260100 ，这篇博客主要讲述利用stringstream进行数据类型转换的方式。
- https://blog.csdn.net/u013066730/article/details/84105567 ，这篇主要讲了整型值和字符串的转换问题，但是只作为参考，比较杂。
- https://www.jianshu.com/p/266fca755967 ，代码很值得参考。
