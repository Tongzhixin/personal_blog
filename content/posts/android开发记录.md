---
title: "Android开发记录"
date: 2021-06-11T15:24:27+08:00
description: "粗糙记录我这学期一门课程设计的作业开发"
tags: 
    - android
    - 课程设计
categories:
    - Development
    - Record
draft: false
---

### 前言

难得静下心来，记录一下本学期进行Android开发中一些踩坑点，以及感悟所得。

我此次参与开发的是一款小型的交友软件，规模只算上一个demo吧，具体地址如下：https://github.com/Rainy-Imola/AppFrontend

<!--more-->

### 经验

#### 一些开源组件的使用和相关博客：

- 标题栏推荐[开源titlebar](https://github.com/loperSeven/TitleBar)，但是这种titlebar总是与CollapsingToolbarLayout里面的toobar不是很吻合，导致出现错位，到现在我也不清楚原因。

- 图片选择[开源](https://github.com/LuckSiege/PictureSelector)，这款还行，不过如果用到fragment上面就只能使用最后的Forresult进行回调。

- 关于coordinatorLayout，十分推荐[此博客](https://segmentfault.com/a/1190000015340856)，写得很详细，而且易懂，我在coordinatorLayout + AppBarLayout + CollapsingToolbarLayout上面花了挺多功夫

  这是仅仅一部分我折叠的标签页，当时开发这块的时候，每天都浏览巨多标签页，根本原因还是没有系统学过Android的UI设计与编写，导致只能自己不断的尝试。

  ![image-20210610173711076](android%E5%BC%80%E5%8F%91%E8%AE%B0%E5%BD%95/image-20210610173711076.png)

- Labelsview推荐[github 开源](https://github.com/donkingliang/LabelsView)

- 星座picker：使用[开源的picker](https://github.com/addappcn/android-pickers);类似的框架还有：[pickerView](https://github.com/Bigkoo/Android-PickerView)

- Navigation可参考[博客](https://blog.csdn.net/yingaizhu/article/details/105972720)，这篇文章浅显易懂的介绍了Navigation的具体过程，能够给予我更深刻的理解。



#### 个人的收获

由于很多开过过程中的细节已经记不太清楚，在此只能记录一些目前仍然印象深刻的点。

- 明确好是否大量使用fragment，我目前比较赞同“一个activity，众多fragment”的开发模式，比较容易跳转，而且仅仅使用少量的Intent进行额外的跳转，在共享数据方面也有很大优势，因为多个fragmen可以共用一个viewmodel，viewmodel的生命周期是与activity相关联的，所以多使用fragment以及共享viewmodel可以减少手机内存使用量，得到更好体验。
- 尽量使用官方目前正在推行的JetPack组件，这次开发中用到了livedata以及navigation，非常的方便，以后开发，希望能够用到databinding，进一步减少ui的麻烦。
- 我们的网络请求框架使用的是Okhttp3，个人用下来感受是挺好用的，但是建议以后使用的时候能够对某一类型加以封装，由于这次并没有封装，我们每次的后端请求都要新写一个网络请求，并对数据包进行解析和回调。如果有机会的话，想多了解其他的网络请求框架
- 在UI方面，我感受到了android开发的奇妙之处，一切皆对象，各种组件都可以进行封装，虽然灵活，但是这也导致了android ui的臃肿，好多组件都需要大量的xml文件配置，写yue了。
- 对于java的接口有了更深刻的理解，这的确是个好东西，能够在使用的时候才去定义这个接口，这样就能够使用到当前环境中的变量，这块与lambda，我还仍需要再研究研究。

#### 仍然有的一些疑惑

这次虽然对android的开发流程有了一定的了解，但是仍然后很多东西我没有接触到。

- 全局UI配置，与activity配置，由于不是我搞的，这块我并不是很熟悉，我觉得android应该有相关的机制，使得很多东西可以像compose组件一样全局配置，而不需要在具体的页面ui中间配置。
- 内嵌浏览器
- 本地存储配置与管理
- 后台服务
- 等等

### 总结

这是我第一次比较完整的参与了较大规模的开发，java语言也没有很多人说的那么臃肿，而且idea确实是编程利器。期末之际，特此记录！

