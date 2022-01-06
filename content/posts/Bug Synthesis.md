---
title: "Bug Synthesis Challenging Bug-Finding Tools with Deep Faults"
date: 2022-01-06T22:18:52+08:00
description: "通过符号执行获得的程序路径，借助约束求解（符号执行），创建状态转移，使程序在一定的输入数据下到达预定的漏洞位置。"
tags: 
    - 论文
categories:
    - 论文阅读
draft: false
---

### 前言
这是一篇看上去复杂，实际上也很复杂的论文，它使用的概念很多、工具很多，尤其是数学上面一些等式逻辑有很多（这可能涉及到SAP和SMP）。正如标题所说，它的目的是为了使那些Fuzzing工具或者符号执行工具如KLEE等进行漏洞挖掘时不那么容易，从而评价一个模型的好坏。看作者的行文，它的目的还在于生成相对于LAVA和EvilCoder更加真实、形式各不相同以及更加难以发现的漏洞。
论文未给出源码，但是给出了工具软件以及测试软件，具体实现可能较为困难。
### 概览
它认为注入的漏洞应该具有以下特点：
-  **Fair** 
-  **Deep** An injected bug is deep if it requires a long sequence of data and control flow conditions to be met for it to rigger. A bug guarded by a single branch condition is, for the same reason, not a challenging bug.
-  **Uncorrelated** Multiple injected bugs must be uncorrelated;that is, finding one of the bugs by a tool should not increase (or decrease) the chances of catching the other injected bugs 
-  **Reproducible** An injected bug must come with a triggering input that proves the existence of the bug.
-  **Rare** The bug should be triggered on a very small fraction of all possible program inputs.
  
  这个新的漏洞注入技术基于符号执行、程序综合、样本一致化，在程序中嵌入Error Transition System (ETS)。通过精心设计的谓词（条件语句），将程序引导至漏洞触发位置。通过模型计数（近似于均匀采样）估计谓词“阻止”到达错误位置的输入集（这一点我没看懂）。最终通过AFL和KLEE工具进行评估并与LAVA进行比较（两者具有相似性，都是经过inputs进行触发漏洞）。

  该ETS的在预先选择的输入（基于符号执行得到的program trace inputs）上执行程序将该状态机逐渐进入错误状态（即，注入的错误将出现的程序点）。只要预先存在的程序变量（在当前位置的范围内）的当前值满足某些条件，就会触发状态机中的转换。可参考下图一看便知。
  ![插入的例子](/assets/Bug%20Synthesis/2022-01-06-18-20-18.png)
  谓词的选择与构建：从轨迹不同点（过渡点）范围内的变量创建候选谓词。并非跟踪中的所有点都有可能成为过渡转换点：扫描跟踪，寻找程序调用图中的程序点，这些程序点由许多分支条件保护，并且范围内有许多变量。为了确保触发每个转换的合成约束满足要求，使用模型计数来估计到目前为止ETS约束连接的解的数量，并通过减少可能解的数量来迭代改进约束集。使用全局程序变量跟踪状态机。对状态机进行编码以匹配主题程序的词汇特征：例如，如果程序主要处理整数值，我们使用整数类型的实体（整数变量、整数数组的元素、结构中的整数字段等）来跟踪状态（这是我们当前原型支持的编码）。

  在每个转换点添加一段代码，该代码检查一个ETS转换谓词，然后相应地推进状态机。当状态机达到其接受状态时，触发（预定义的）错误行为。

### 算法
![概览](/assets/Bug%20Synthesis/2022-01-06-21-23-28.png)
#### Identify a Program Trace
使用符号执行探索，采集程序可能路径，然后选择一个拥有以下特点的路径：
- 复杂路径。包含大量动态指令，过程和分支指令
- 变量的质量和数量。变量的质量取决于在程序依赖关系图中定义变量的指令与输入语句之间的距离。选取具有大量高质量的变量的路径。

#### Identify Transition Points in the Program
找到嵌入ets系统的位置；
拥有以下特点：
- 在该程序位置有丰富的“有用”变量
- 程序位置在调用图中很深，使得bug检测工具很难到达该位置
- 程序位置出现在控制依赖关系图的深处；控制依赖关系图中的深层位置由多个谓词保护

#### 收集符号约束
在之前选择的trace上面运行符号执行引擎，获取符号约束
 - Symbolic Path Condition (sym_pc):
 - Symbolic Expression Dictionary (Λ): This dictionary Λ : L 7→ (V 7→ E) maps each identified transition location li ∈ L in the program to a dictionary of symbolic expressions E for each program variable v ∈ V
 - Concrete Value Dictionary (C): This dictionary C : L 7→ (V 7→ ν) maps each identified transition location li ∈ L in the program to a dictionary of concrete values ν observed for each program variable v ∈ V along the execution trace

#### Synthesize the Error Transition System (ETS)
使用约束求解来合成一个可以嵌入到程序中的错误转换系统（ETS）。
ETS的合成本质上涉及到保护自动机转换的转换谓词的识别。
谓词应该满足一下属性：
![谓词条件](/assets/Bug%20Synthesis/2022-01-06-22-07-12.png)
这是统一的公式，不过看不太懂。
![公式](/assets/Bug%20Synthesis/2022-01-06-22-11-07.png)
谓词条件的各项应该满足：
- 第一项确保进入ets系统内；
- 第二项确保仅仅只有少数inputs才能到达这里
- 接下来的一项仅仅确保能够被inputs触发即可
- 最后一项确保能够执行自己创造的分支进入注入模块；

通过从错误诱导区域中搜索另一个点P2来进一步缩小错误诱导区域，这样新的谓词将触发器与P1和P2分开。为了估计合成谓词的有用性，我们在对应于每个位置的多变量谓词空间上执行hill climing搜索（第16-19行）。该搜索使用模型计数器来估计与新合成谓词和旧谓词（缓存在pred[k]中）对应的可行输入的数量；我们选择了一个谓词，它最大限度地缩小了引起bug的输入的空间。搜索的设计类似于多变量问题的Gibbs采样器[9]，其中我们根据每个其他变量的当前值决定一个变量。由于在每次迭代中调用两次模型计数器非常昂贵，在我们的实现中，我们通过在sym_pc上执行统一采样来创建与种子输入路径相同的输入（测试）采样空间，从而近似谓词的相对有用性。

### 评估
- 符号执行KLEE
- 灰盒测试AFL

LAVA 注入的 bug 通常对某个工具(KLEE)表现出亲和力。通过一组注入的 bug，每个 bug 可能对某个工具表现出亲和力，但总的来说，工具注入的所有 bug 都应该是公正的。对于 Apocalypse 注入的 bug，尽管某个工具可能比其他工具更容易发现某个 bug，但总体来说，两个工具在发现我们工具注入的 bug 方面几乎同样有效(KLEE 发现的 bug 占30% ，AFL 在这9个程序中发现的 bug 占47%)。这表明 Apocalypse 合成的bug比 LAVA 注入的虫子更“自然”，因为它们没有表现出可以被虫子发现工具利用的“人工”属性。

### 总结
虽然提供了一个较为完整的利用符号执行以及添加路径信息创建漏洞的方法，但是并没有插入漏洞，仅仅使用assert(0)代替，可能论文作者不屑于干这件事情，仅仅是为了羞辱LAVA（哈哈哈）。
这篇论文在算法上面很难读懂，使用了离散数学上面的一些知识而且解释的不是很清楚。但是给了我一个很好的启示：由于他没有写完整，我可以简化一下他的步骤，同时加上漏洞模板的插入，可能更加有助于增加漏洞样本注入的真实性与多样性。