---
title: 使用百度API来开发小程序的可能性
date: 2023-08-07 09:35:30
categories: 
- 学习
---

### 前言

[前文](https://blog.cindyforever.com/2023/07/21/2023/07/do-not-develop-in-wechat/)说过，个人开发小程序的最大的几个问题：
- 个人可申请的开发类目有限，可用的几个都看不到太大的变现可能
- 但凡用到后台服务器功能的情况下，你只能申请国内的服务器，国内域名和国内的ssl证书，这三笔费用加起来粗略估计比国外贵10倍以上
- 哪怕你不在乎花钱，做到了第二点，你会发现，国内的服务器无法访问大量的国外网站，也就导致很多简单易用的api和数据都拿不到

基于上述情况，我在那篇文章提到用云托管+国外服务器的方式来解决，后面发现也不是特别行得通，至于细节不再赘述，那篇文章也说的很清楚。

但是后面我不甘心还是在小程序上发布了一个可有可无的应用，一个预报天气的应用，这个完全不用后台，只用了国内网站API的应用启发了我：我何不利用国内大厂的API来处理我的需求，这样既不用花服务器的钱，又不用担心服务器处理能力不足，而且通常也有免费额度，适合新手个人开发来进行试错。

### 百度API

[百度API的文档](https://cloud.baidu.com/doc/index.html)详细罗列了可用的几个可用的API，没办法一一罗列，值得注意和引起我兴趣的几个大类是：人工智能，智能视频，智能大数据。

我也提到了个人开发在小程序可发布的分类不多，但是有个分类可操作性挺大，就是**办公**。这个是定义比较模糊，也算是上班人员比较刚需的一大类。是个人开发少数能玩出花来，也能有一点变现潜力的分类。

所以结合小程序的办公分类加上百度API，我们来看看能不能开发一个小程序。

### 功能和价格

这边还是先不评价具体的价格和类目，就简单罗列一下我认为有用的API和价格，作为今后开发的参考。后面的价目是按照其分类里面的某一个大致写了下，毕竟类目实在太多，无法都写上。

|   功能   |   功能描述   |  免费额度   |   费用   |
| ----------- | ----------- | ----------- | ----------- |
| 语音识别      | 将语音转换为文字       |     无        |  2400元/百万次  0.0034元/次|
| 文字识别   | 将图片中的文字识别为文本        |      1000次/月       | 50元/万次   0.005元/次 |
| 图像识别   | 获取图片中的物体或人物信息        |      1万次/365天（一次性）       | 29元/万次/一年   3元/千次 |
| 图像增强与特效   | 优化或修改图像效果        |      1000次/365天（一次性）       | 45元/千次 |

### 分析可行性

我可以认为百度是个周扒皮。。。大部分已经没啥卵用的api，比如文字识别，也不过给了1000次每月的免费额度，其他的功能都是一年以内非常有限次数的免费调用。之后就是贵的要死的价格，我个人认为试错是可以，但是如果真的用了百度API的免费额度搞出了业务量，还是最好把调用服务放到自己的后台，不然实在是太贵。