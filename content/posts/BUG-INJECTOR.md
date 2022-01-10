---
title: "Automated Customized Bug-Benchmark Generation"
date: 2022-01-09T19:25:00+08:00
description: "通过动态分析采集漏洞插入点，然后根据制作的漏洞模板进行插入点上下文的匹配，从而将漏洞模板与宿主源程序进行融合生成漏洞样本。"
tags: 
    - 论文
categories:
    - 论文阅读
draft: false
---

### 前言
  我花了一整个下午阅读和标记这篇文章，虽说没有上一篇BUG Synthesis那么难以理解，但很多过程仍然是抽象的。
  这篇文章发表于SCAM，在CCF上面查看似乎是一个C类会议，但是我觉得这篇文章其实做了不少工作。
  论文提出了一个以漏洞模板、代码综合（融合）、动态分析为基础的基准测试集生成方法，生成的测试集包含测试用例以及漏洞信息。难点在于对程序进行动态轨迹分析、漏洞模板定义与构造、将漏洞模板与漏洞注入点进行上下文环境的融合。
  
### 简述
  好的评估系统将通过识别缺陷发现工具的盲点，指导漏洞分析工具的有效改进。
  使用Clang Static Analyzer and Infer进行最终评估。采用recall（回调率，也即发现的漏洞占据所有漏洞的比例）对生成的基准测试集进行评估。
  使用动态追踪来识别漏洞注入位置，而不是使用静态分析中的信息，可以不受限于偏见和静态分析技术（例如指针分析不精确或SMT解算器缺陷。之后根据BUG模板插入BUG，并与现有数据和控制流集成，从而创建宿主程序的新变体。

### 过程

#### 工具使用
  Software Evolution Library (SEL) The Software Evolution library enables the programmatic modification and evaluation of extant software. C/C++ software modifications are implemented via Clang’s libtooling API.Clang’s libtooling provides a solid foundation for parsing and program modification in the presence of the latest C/C++ syntactic features。简单来说就是可以通过这个工具进行正确的修改以及动态分析。
  
#### 漏洞模板
  本篇论文中的漏洞模板由Common Lisp语言定义（挺复杂的）。漏洞模板由三者组成：
  - 成功的bug注入的动态和静态要求
  - 构成bug本身的代码片段
  - 这些代码片段应该如何集成到程序中。
   
  漏洞模板由一个或多个补丁组成。（不理解）
  漏洞模板有两个来源
  - 待评估漏洞分析工具专门针对的漏洞类型的特征
  - 根据已有的传统漏洞数据集进行手动提取创建，可以得到多种类型的漏洞模板

  漏洞模板构造需要识别：
  - 相关bug代码 
  - bug代码中必须在宿主程序中重新绑定的自由变量
  - 确保bug成功转移到宿主程序的先决条件。
#### precondition先决条件
使用布尔谓词来表达符合漏洞插入点的前提条件。这部分与sel的使用密切相关。
 - value($v, p): the value of $v at p
 - size($v, p): the dynamically allocated size of memory pointed to by $v at p
 - ast(p): the abstract syntax tree at p
 - name($v): the name of $v
 - type($v): the static type of $v
![漏洞模板](assets/BUG-INJECTOR/2022-01-09-20-12-39.png)

#### 执行过程

![执行过程](assets/BUG-INJECTOR/2022-01-09-20-19-16.png)
  - Instrument方法重写主机程序的源代码，插入代码以进行动态跟踪。（sel）
  - Execute 进行动态分析，跟踪包括每个程序语句中所有范围内变量的值（目前仅限于基本类型和指针）。该算法使用测试输入运行经过instrument的程序，通过之前已有的先决条件找到漏洞插入点，并将其存储在数据库中。
  - 根据可插入点的数据库和相应的漏洞模板进行宿主源程序的改写，源代码重写涉及将关联的代码段插入宿主程序，然后使用宿主程序的前置条件匹配和类型兼容的作用域内变量重命名所有自由变量名。
  - bug触发验证

  ![example](assets/BUG-INJECTOR/2022-01-09-20-35-09.png)

#### 评估
以grep和nginx为宿主源程序
漏洞模板为两个来源，从而形成两个基准测试集，分别进行对比
与LAVA进行对比
对不同的静态分析工具以及不同的参数进行对比
LAVA得到的数据集更加适合对Fuzzing工具进行测试，CSA和infer两个工具都找不着根据LAVA生成的数据集中的漏洞。

### 总结
论文作者想要做并发相关的bug生成，以及考虑平衡注入的bug数量等因素，代码的自然性bug的真实分布，保留原始程序行为功能，以及buggy程序和原始程序之间的语法/风格相似性。SEL支持具有多目标适应度函数的进化搜索。
论文中举的例子不够恰当，所举例子跟源宿主程序上下文基本无关联。











