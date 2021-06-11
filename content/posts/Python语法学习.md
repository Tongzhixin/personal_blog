---
title: "Python系统学习时的一些记录"
date: 2020-03-07T15:24:27+08:00
description: "可能是要大规模使用吧，当时在菜鸟教程上以及官网上进行了系统的语言学习，现在（2021年6月）看起来有些不值得，粗糙记录，以后有更新还会往里加"
tags: 
    - Python
    - 编程语言
categories:
    - Development
    - Record
    - 编程语言
draft: false
---

-------

update： 我感觉自己写的太烂了！！！

--------

1. ##### 函数默认参数只会执行一次

   eg：

   ```
   def f(a, L=[]):
       L.append(a)
       return L
   
   print(f(1))
   print(f(2))
   print(f(3))
   打印出[1]
   [1, 2]
   [1, 2, 3]
   ```

   这时要改为

   ```
   def f(a, L=None):
       if L is None:
           L = []
       L.append(a)
       return L 就可以了
   ```

2. 形参特别：当存在一个形式为 `**name` 的最后一个形参时，它会接收一个字典 (参见 [映射类型 --- dict](https://docs.python.org/zh-cn/3.8/library/stdtypes.html#typesmapping))，其中包含除了与已有形参相对应的关键字参数以外的所有关键字参数。 这可以与一个形式为 `*name`，接收一个包含除了与已有形参列表以外的位置参数的 元组 的形参组合使用 (`*name` 必须出现在 `**name` 之前。)

3. 仅限位置形参和仅限关键字形参    /之前时仅限位置    *之后时仅限关键字形参

4. 函数标注：形如`def f(ham: str, eggs: str = 'eggs') -> str:`估计用不到。。。

5. 编译过的python文件是指载入速度更快

6. 在制作包时，记得_`__init__`.py文件，如果想要在import*时,有自己的定义，那么可以在___init___.py中增加____all____属性，这里由于md语法，没能是两个下滑线；从子包中导入时，可能会有相对路径，但是记得不能用在主程序中，划重点。。

