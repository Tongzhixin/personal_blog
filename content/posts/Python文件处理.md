---
title: "利用Python解决一个实际问题"
date: 2020-02-08T15:24:27+08:00
description: "记录我在利用Python解决问题时遇到的一些坑，以及相应的解决方式"
tags: 
    - Python
    - 解决问题
categories:
    - Development
    - 解决问题
    - Record
draft: false
---

## 声明：请不要全部借鉴，可能会有问题！！！

#### python文件处理

首先，任务是模拟编译器进行预处理。拿我自己的cpp文件做示例，我的想法是：以#include"name"为中间线将该文件分为中间线之前加上中间线所蕴含的东西以及中间线之后的东西，由于一个文件中可能包含多个头文件，所以用一个for语句搭配列表进行使用。

<!-- more -->

打开文件之后要一行一行的读取，使用进行整个的包含。

```
lines=[]
for line in file:
	lines.append(line)
```

然后回归到原点`f.seek(0)`，之后就是正则化寻找`re.search(r')`，期间使用了

```python
#!/usr/bin/env python3.7
import re,os

def move_file(str_load,loaded_list):
    flag=False
    count=0
    lines_me=[]#用来保存分割后的原文件的内容，含有len（count_list)+1段内容
    count_list=[]#用来保存读到的含有正则表达式的段落
    lines_list=[]#用来保存#include""的内容，包含递归的内容
    #f.seek(0)
    f=open(str_load,'r',errors='replace')
    for line_f in f:
        lines_me.append(line_f)#读进去，其实我想跟下面的那个for循环搁一起也没有关系
    f.seek(0)
    for line in f:
        count=count+1#记录行数
        searchobj=re.search(r'#include\s+"(.*?)"',line)#正则匹配
        if searchobj:            
            f1_name=searchobj.group(1)#打印出来匹配的名字
            if f1_name in loaded_list:#我加上的这个if语句是为了防止出现不能找到包含的头文件的状况
                flag=True
                lines_list.append(['\n'])
                count_list.append(count)
                print(f1_name)
            else:
                loaded_list.append(f1_name)#如果还未曾出现，就加入这个loaded_list列表里
                for relpath, dirs, files in os.walk('/home/tzx/Desktop/testprp'):#遍历整个文件目录
                    if f1_name in files:
                        flag=True
                        full_path = os.path.join('/home/tzx/Desktop/testprp', relpath, f1_name)
                        print(full_path)
                        #f2=open(full_path,'r+')
                        lines_list.append(move_file(full_path,loaded_list))#包含递归的过程
                        #f2.close()
                        count_list.append(count)
                print(count_list)
    if flag:#如果出现分割，就执行下面的语句
        lines_me_list=[]
        it=iter(count_list)
       # print(count_list)
        count_temp=0
        for x in it:
        #    print(x)
            lines_me[x-1]='/*'+lines_me[x-1].strip('\n')+'*/'+'\n'#是为了加入注释符
            lines_me_list.append(lines_me[count_temp:x])
            count_temp=x
        lines_me_list.append(lines_me[count_temp:count])
 #       print(lines_me_list)
        it2=iter(lines_me_list)#迭代器使用，较为方便
        lines_me_you=next(it2)
 #      print(next(it2))
        it3=iter(lines_list)
        it1=iter(count_list)
        count2=0
        for y in it1:
            lines_me_you=lines_me_you+next(it3)+next(it2)

        return lines_me_you#返回整理后的字符串对象

    else:
        return lines_me#这是如果不存在分割，就会返回原来文件的内容




#f1=open('/home/tzx/Desktop/testprp/scpc_main.c',r')
#f2=open('/home/tzx/Desktop/sLinkList.i','w')
#f2.writelines(move_file('/home/tzx/Desktop/testprp/scpc_main.c',[]))
#f2.close()
#f1.close()
#testprp/scpc_main.c

road='/home/tzx/Desktop/iii/'#为了简化使用，是最后写入的路径，可灵活调整
root='/home/tzx/Desktop/testprp'#为了简化使用，当然也可以作为参数使用，如果作为参数使用就需要更改一下函数
for relpath, dirs, files in os.walk('/home/tzx/Desktop/testprp'):
    for name in files:
        if re.match(r'(.*)[.]c',name):#对所有需要预编译的函数进行正则匹配
            name2=road+name[0:-1]+'i'
            with open(name2,'w') as f2:
                f2.writelines(move_file(os.path.join(root, relpath, name),[]))#写入
```

整个过程中，麻烦的点：

- 参数的确定。一开始的参数是返回的文件句柄，后来由于解码问题和递归问题才使用路径的字符串作为一个参数。而另外一个参数只是为了防止重复写入同一个问件多次而设立的列表。
- 解码问题。由于文件中出现了不同于utf-8的编码方式，就会出现不同程度的乱码问题。查了很多资料，有很多解决办法，因为打开文件进行读取时，python默认的有相应的编码解码错误处理方式，在open（file，model，arg）arg就是很多其他的参数，其中，默认errors=‘ ‘的参数是报错的意思，这时候，你可以加入python默认的一些错误处理参数，比如’replace‘就可以将所有的乱码替换为"?"。[附连接](https://docs.python.org/zh-cn/3/library/codecs.html#codecs.replace_errors)。（这是python文档链接）
- 行数计数问题。一开始以为有内置的处理函数，查了一下好像有一个`row=sys._getframe().f_lineno`，可是经过我的实验，这个函数没毛用，一直返回1.似乎是返回的整个模块调用的行数。最后老实的用了传统的方法。
- 正则匹配问题。这里面，我用到了`re.search`and `re.match`两个函数，都有各自的特点，网上说的很明白。还有一点感悟就是，本来觉得挺难的，看多了之后觉得自己似乎也能弄出来一个简单的正则表达式，看来熟能生巧啊。
- 路径遍历问题，这里我使用的是os模块的函数os.walk()函数，很好用。返回的参数也很有帮助。
- 迭代器使用。这个迭代器真的好用，夸夸夸！
- 列表。这里感谢`append()`函数和`pop()`函数，真的好用，这也就是为什么python要比c++好用一点的原因吧，虽然c++也有相应的stl库，但是为什么不学啊。。。。。
- 字符串。这里使用的是`str.strip()`函数，这个函数比较特殊，能够处理首尾两端的问题，看到这里可以看一下python文档。字符串是不可更改对象，一般只能更改返回的句柄好像。
- None.match函数未匹配成功就会返回的值，如果if就相当于False。
- 报错：其实这后半段麻烦的点我已经写了一遍了，刚刚不知道为什么就摁了某个键就全部撤销了，那一刻好想杀人啊。。。。
  - 报错1：读取范围错误，很多情况可能是你的对象不对，因为一开始我的count_list[]是在for循环内定义的，导致了这个报错，后来才意识到，我真的sb。
  - 报错2：编码错误。这个就是open里面默认的参数errors的缘故，更改为`errors='replace'`就好了，全部替换为'?'
  - 报错3：不可预估的错误。大多是由于缩进的缘故。
  - 报错4：重复写入。就是我一开始没有加入loaded_list之前，那叫一个。。。一个文件里写了好几千行。。。就是因为很多文件重复性包含，很想知道gcc编译器是怎么样预处理的

对了，记录一下没有写这个脚本之前，我的想法叭。

#### gcc

一开始，我打算使用gcc的各种神命令，进行预编译的操作，写一个shell脚本，可能会 更简单。然而我想多了。附链接博客[预处理详解](https://www.cnblogs.com/lulipro/p/5976601.html).[命令](http://c.biancheng.net/view/2375.html),更多命令参见gcc的[文档](https://gcc.gnu.org/onlinedocs/gcc-7.5.0/gcc/Directory-Options.html#Directory-Options)，更加详细,只是这个内容的文档。

在试了gcc -E的诸多命令之后，包括`-no……`那个命令，就是不遍历标准库的头文件，但是就是一直报错，因为没有找到这个文件，这是太难了，我也不知道gcc怎么跳过这样的错误。另外记录一下，另一个命令-iquote dir 就是优先搜索这个文件里的头文件，本来以为有这两个命令之后就可以了。没成想，有的文件会找不到，命令没法继续。而自己写的复制脚本能解决这些问题。

然而很头疼的是，不知道如何处理众多的#if……#endif……还有#elseif，真的是太难了。



##### 结尾

通过两天的不懈学习，知识有了很多的增加，知道了python的好用，和正则表达式，以及python的部分数据结构。果然，知识在磨练中会增加更快。

还有，网上的博客果然良莠不齐，让人头大，刚一接触，觉得每个博客都很神奇，当了解了一定内容后，感觉这些博客似是而非。好像就是我这样的，不过我只是用来记录生活和技术的。

注：（昨天学长告诉我，这个小小任务没有那么急，所以那一点点的困难就交给学长处理叭，摸鱼开森！）