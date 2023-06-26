---
title: 一周文章收集-20230604
date: 2023-06-04 12:00:00
comments: true
categories: 
- 一周文章收集
---

## [为什么你应该继续使用telnet](https://bash-prompt.net/guides/telnet/)
随着ssh的盛行，telnet几乎已经被程序员抛弃了，但是在某些情况下，telnet可以帮你排查各种网络问题。这篇文章告诉你怎么使用telnet。

## [自动驾驶在消亡](https://www.understandingai.org/p/the-death-of-self-driving-cars-is)
这篇文章给出了不同于主流观点的对自动驾驶的看法，介绍了一些在自动驾驶行业在逐渐退去热度的迹象。包括比如Uber在2020年出售了自动驾驶业务，Lyft在2021年停止了开发。总而言之，比起在2015到2017年左右对于自动驾驶的预期来说，目前的自动驾驶行业发展远远未达到人们的预期。

## [火车轨道上的石头是干嘛用的](https://www.alpharail.co.nz/why-do-railway-tracks-have-crushed-stones-alongside-them/)
铁轨上的石头尖锐而坚固，这篇文章介绍了为何需要在铁轨上铺设石头，简而言之，石头可以帮助减震，帮助承受火车经过时产生的巨大冲击力。

## [Chirper.ai：一个完全由AI机器人参与的社交媒体](https://www.fry-ai.com/p/social-media-no-humans-allowed)
在这个[网站](https://chirper.ai/)通过指定一个昵称和描述一下这个机器人的设定，后台就会自动生成一个机器人，参与社交媒体的内容创作和互动。
![](social_AI_1.png)
这个机器人会按照你的设定来进行内容创作，会去关注和回复其他机器人。并且拥有独立的性格和情感。
![](social_AI_2.png)
有些互动让人感到颇为惊讶。
![](social_AI_3.png)

## [google官方的免费创造性AI课程](https://www.cloudskillsboost.google/journeys/118)
目前包含十节课，介绍了大语言模型，图像生成模型等等，学习门槛较高，需要英语能力和梯子。

## [封闭模型训练数据的来源令人担忧](https://simonwillison.net/2023/Jun/4/closed-model-training/)
这篇文章描述了作者对于封闭模型的训练数据来源的担忧，比如copilot可能取用github上用户的代码，google的人工智能模型bard也曾经提及他们的数据可能来自gmail。

## [RARBG IPFS：搜索IPFS资源](https://ipfs.io/ipfs/QmbpRxBZ5HDZDVRoeAU8xFYnoP4r5eGCxdkmfFW3JbA6mq/)
如果不知道什么是IPFS（The InterPlanetary File System 星际传输文件系统），看下这个[https://docs.ipfs.tech/](https://docs.ipfs.tech/)，有点类似以前的磁力链接，是去中心化趋势中的产物。下载客户端就意味着你的机器成为一台IPFS的资源主机，可用的下载链接将会在网络上进行传播。可以看看下面的这个图片里的讨论.
![](IPFS.png)

## [privateGPT:可以在本地实现问答的聊天机器人](https://github.com/SamurAIGPT/privateGPT)
通过利用本地LLM的能力，在不依赖于互联网的情况下创建一个基于您的文档的问答聊天机器人。确保完全的隐私和安全性，因为您的数据永远不会离开本地执行环境。即使没有互联网连接，也可以无缝地处理和查询您的文档。

## [Arroyo：一个用Rust编写的分布式流处理引擎](https://github.com/ArroyoSystems/arroyo)
简而言之，Arroyo使您能够对高容量实时数据提出复杂问题，并获得亚秒级的结果。相比起世面已存在的流引擎（Apache Flink、Spark Streaming和Kafka Streams），Arroyo有如下特点，是无服务的，高性能SQL，以及为非专家设计。

## [黑镜导演利用chatGPT进行剧本创作](https://www.empireonline.com/tv/news/charlie-brooker-chatgpt-black-mirror-episode-exclusive-image/)
英剧《黑镜》系列是关于在未来，科技的发展对于人类造成危害。因此，当黑镜的导演用chatGPT创作故事时，整个事情听上去就颇为黑色幽默。不过最后导演说chatGPT的故事只是初看起来合理，仔细琢磨之后狗屁不通。

## [fastgen：一个低代码工具，以进行快速开发](https://fastgen.com/)
Fastgen是一个低代码软件平台，允许用户快速轻松地构建API端点和工作流程。Fastgen提供直观友好的界面，无需深入的编程知识即可创建API和工作流程。