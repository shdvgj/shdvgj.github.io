---
title: 每日文章收集-20230719
date: 2023-07-19 09:59:25
categories: 
- 每日文章收集
---

### [Stable Diffusion的一个网页DEMO](https://islamov.ai/stable-diffusion-webgpu/)

![](sd1.png)
这个网页使用的是你本地的内存和GPU来生成AI图片，根据网页介绍：
> 每个步骤大约需要1分钟+额外的约10秒来运行VAE解码器生成图像。打开DevTools会将所有操作减慢到大约2倍的速度。UNET仅在CPU上运行（速度快10%，在GPU上无法正确生成结果），因此会导致浏览器标签卡顿。
> 
> 为了得到可接受的结果，最少需要20个步骤。然而，对于演示目的，3个步骤就足够了。
> 
> 模型文件将被缓存，因此您无需每次下载它们。

另外需要最新版本的chrome，并打开`Experimental WebAssembly`和`Experimental WebAssembly JavaScript Promise Integration (JSPI)`的设置。

为了生成图像，这个网页还需要下载3.5gb的数据和耗费8gb的内存。

![](sd2.png)

### [lazydocker：管理Docker的懒人方式](https://github.com/jesseduffield/lazydocker)

![](lazydocker1.png)
这是一个简单的终端UI用于管理docker容器和镜像。

#### 前提

- Docker 版本 >= 1.13 (API >= 1.25)
- Docker-Compose 版本 >= 1.23.2 (optional)

#### 安装

有多种不同的[安装方法](https://github.com/jesseduffield/lazydocker#installation)，最通用的方法如下：
```sh
curl https://raw.githubusercontent.com/jesseduffield/lazydocker/master/scripts/install_update_linux.sh | bash
```

#### 用法

使用命令`echo "alias lzd='lazydocker'" >> ~/.zshrc`来展示lazydocker界面。

#### 主要feature
- 一目了然地查看Docker或Docker Compose容器环境的状态
- 查看容器/服务的日志
- 查看容器指标的ASCII图表，以便不仅感觉像开发人员，还能看起来像开发人员
- 自定义这些图表以测量几乎任何您想要的指标
- 附加到容器/服务
- 重新启动/删除/重建容器/服务
- 查看给定镜像的祖先层
- 清理占用磁盘空间的容器、镜像或卷




