---
title: linux版本过低情况下使用高版本nodejs
date: 2021-12-17 11:13:26
tags:
- linux
comments: true
categories: 
- 学习
- 运维
---

**我这里针对的低版本linux指的是centos6。**

## 问题起源

最近需要写一个页面，我前段时间一直在用nodejs开发，所以我就选择了react来写页面。在本机开发调试完成之后，部署到linux上出现问题，在运行npm install命令的时候出现如下报错：
```shell
npm WARN notsup Unsupported engine for react-scripts@5.0.0: wanted: {"node":">=14.0.0"} (current: {"node":"10.24.1","npm":"6.14.12"})
npm WARN notsup Not compatible with your version of node/npm: react-scripts@5.0.0
npm WARN notsup Unsupported engine for css-loader@6.5.1: wanted: {"node":">= 12.13.0"} (current: {"node":"10.24.1","npm":"6.14.12"})
npm WARN notsup Not compatible with your version of node/npm: css-loader@6.5.1
npm WARN notsup Unsupported engine for css-minimizer-webpack-plugin@3.3.1: wanted: {"node":">= 12.13.0"} (current: {"node":"10.24.1","npm":"6.14.12"})
npm WARN notsup Not compatible with your version of node/npm: css-minimizer-webpack-plugin@3.3.1
npm WARN notsup Unsupported engine for eslint@8.5.0: wanted: {"node":"^12.22.0 || ^14.17.0 || >=16.0.0"} (current: {"node":"10.24.1","npm":"6.14.12"})
npm WARN notsup Not compatible with your version of node/npm: eslint@8.5.0
.....
```
显然，根据提示是因为node版本太低导致，那首先想到的必然就是升级node版本。

## 尝试升级node版本失败，gcc版本过低

升级node版本有几种办法：

- 通过下载[官网](https://nodejs.org/en/download/releases/)的二进制包，然后解压缩到目录，再通过ln指令进行node版本的关联。
- 第二种方法比较简单，安装node的一个版本管理工具，叫[n](https://github.com/tj/n)，然后运行如下指令就可以完成安装：
```shell
npm install -g n
n node版本号
```
<!-- more -->
然而在我运行了n 14 之后，却出现了报错如下：
```shell
node: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.14' not found (required by node)
node: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.18' not found (required by node)
node: /usr/lib64/libstdc++.so.6: version `CXXABI_1.3.5' not found (required by node)
node: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.15' not found (required by node)
/usr/local/bin/node: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.14' not found (required by /usr/local/bin/node)
/usr/local/bin/node: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.18' not found (required by /usr/local/bin/node)
/usr/local/bin/node: /usr/lib64/libstdc++.so.6: version `CXXABI_1.3.5' not found (required by /usr/local/bin/node)
/usr/local/bin/node: /usr/lib64/libstdc++.so.6: version `GLIBCXX_3.4.15' not found (required by /usr/local/bin/node)
```
这个报错我去网上搜了下，原因基本就是因为gcc的版本过低导致。在解决这个问题的时候，我出现了非常多的路线错误，导致这个问题本来挺容易解决的，却绕了很多的弯。

## 尝试升级gcc版本失败，yum安装源版本过低
首先我直接尝试了运行yum安装命令：
```shell
yum -y install gcc
```
结果报错，提示404。经查发现原来centos已经停止支持centos6版本的linux了，甚至还卸磨杀驴，将之前的yum源也都关闭，不仅如此，国内的所有centos6的镜像yum源也都关闭了。最终我在阿里的镜像源里看到一句话，如果非要用centos6的yum源，访问[https://vault.centos.org/](https://vault.centos.org/)。看来centos还是归档了一份镜像源，还算有点良心，但是这个网站也写了下面一段话。
> This is _NOT_ an updated tree for installing CentOS Linux : It is a snapshot of the older trees that have been removed from the main CentOS servers as new point releases are released.
  This is provided for reference and to provide access to older archived versions, and we do not put security updates into the trees on this server.
  Please see this [link](http://www.centos.org/download) for active versions of CentOS Linux 
  
总之就是告诉你这个镜像也尽量别用，只是为了老版本linux做的一个备份而已。反正centos意思也很明显了，centos6你就别用了，能升级就升级。
那有什么办法呢，先用这个镜像源看看吧。尝试替换yum源：
```shell
## 最好是先备份一份，我只是为了复盘问题处理过程，所以省略这一步
vi /etc/yum.repos.d/CentOS-Base.repo
```
可以直接用如下内容替换进去：
```properties
#注意，这是centos6的yum源，更高版本的不要用这个源
[base]
name=CentOS-6.10 - Base - vault.centos.org
failovermethod=priority
baseurl=https://vault.centos.org/6.10/os/$basearch/
gpgcheck=1
gpgkey=https://vault.centos.org/RPM-GPG-KEY-CentOS-6

#released updates
[updates]
name=CentOS-6.10 - Updates - vault.centos.org
failovermethod=priority
baseurl=https://vault.centos.org/6.10/updates/$basearch/
gpgcheck=1
gpgkey=https://vault.centos.org/RPM-GPG-KEY-CentOS-6

#additional packages that may be useful
[extras]
name=CentOS-6.10 - Extras - vault.centos.org
failovermethod=priority
baseurl=https://vault.centos.org/6.10/extras/$basearch/
gpgcheck=1
gpgkey=https://vault.centos.org/RPM-GPG-KEY-CentOS-6

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-6.10 - Plus - vault.centos.org
failovermethod=priority
baseurl=https://vault.centos.org/6.10/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://vault.centos.org/RPM-GPG-KEY-CentOS-6

#contrib - packages by Centos Users
[contrib]
name=CentOS-6.10 - Contrib - vault.centos.org
failovermethod=priority
baseurl=https://vault.centos.org/6.10/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://vault.centos.org/RPM-GPG-KEY-CentOS-6
```
这样一通操作之后，通过yum安装gcc倒是走通了，可是。。。安装的版本还是不符合要求，这个yum源下的gcc最高版本还是太低。那咋办？我想到了两个方式，一是手动安装，二是。。。升级linux版本。

## 尝试手动安装高版本gcc失败
过程不再赘述了，基本原因是linux内核版本太低，使得make命令执行失败。

## 升级lunux版本失败
升级linux我确实也去查了下方法，升级失败的原因也挺操蛋的，就是centos全面关闭centos6的支持，甚至连centos6升级到centos7的通道都关了，导致网上查的那些方法都不能用，centos真是全面诠释了什么叫全面关闭对老系统的支持。

## 回归最早的报错，解决`'GLIBCXX_3.4.20' not found`的问题
上面解决问题的过程持续了几乎一整天，我在linux版本升级也失败之后，我突然意识到，我只是想要部署个web页面而已啊，为啥会搞的这么麻烦？于是我回归到最早的那个报错，`'GLIBCXX_3.4.20' not found`，在百度上连续吃瘪之后，我用了谷歌来查这个问题，没想到的是，谷歌出来的一片文章五分钟就解决另外我的问题。
这篇文章链接：[version 'GLIBCXX_3.4.20' not found 解决方法](https://www.jianshu.com/p/050b2b777b9d)。
为了防止原文404，我也备份一下这个解决办法：
```shell
1. 查看系统版本
strings /usr/lib64/libstdc++.so.6 | grep GLIBCXX
GLIBCXX_3.4
GLIBCXX_3.4.1
GLIBCXX_3.4.2
GLIBCXX_3.4.3
GLIBCXX_3.4.4
GLIBCXX_3.4.5
GLIBCXX_3.4.6
GLIBCXX_3.4.7
GLIBCXX_3.4.8
GLIBCXX_3.4.9
GLIBCXX_3.4.10
GLIBCXX_3.4.11
GLIBCXX_3.4.12
GLIBCXX_3.4.13
GLIBCXX_3.4.14
GLIBCXX_3.4.15
GLIBCXX_3.4.16
GLIBCXX_3.4.17
GLIBCXX_3.4.18
GLIBCXX_3.4.19
GLIBCXX_DEBUG_MESSAGE_LENGTH

发现少了GLIBCXX_3.4.20，解决方法是升级libstdc++.

2. 
sudo yum provides libstdc++.so.6
Loaded plugins: fastestmirror, langpacks
Determining fastest mirrors
libstdc++-4.8.5-39.el7.i686 : GNU Standard C++ Library
Repo        : base
Matched from:
Provides    : libstdc++.so.6

3. 
cd /usr/local/lib64
# 下载最新版本的libstdc.so_.6.0.26
sudo wget http://www.vuln.cn/wp-content/uploads/2019/08/libstdc.so_.6.0.26.zip
unzip libstdc.so_.6.0.26.zip
# 将下载的最新版本拷贝到 /usr/lib64
cp libstdc++.so.6.0.26 /usr/lib64
cd  /usr/lib64
# 查看 /usr/lib64下libstdc++.so.6链接的版本
ls -l | grep libstdc++
libstdc++.so.6 ->libstdc++.so.6.0.19
# 删除/usr/lib64原来的软连接libstdc++.so.6，删除之前先备份一份
sudo rm libstdc++.so.6
# 链接新的版本
sudo ln -s libstdc++.so.6.0.26 libstdc++.so.6
# 查看新版本，成功
strings /usr/lib64/libstdc++.so.6 | grep GLIBCXX
...
GLIBCXX_3.4.18
GLIBCXX_3.4.19
GLIBCXX_3.4.20
GLIBCXX_3.4.21
GLIBCXX_3.4.22
GLIBCXX_3.4.23
GLIBCXX_3.4.24
GLIBCXX_3.4.25
GLIBCXX_3.4.26
GLIBCXX_DEBUG_MESSAGE_LENGTH
...
```
其实这个方法也算是手动安装，但是问题的关键是他的方式是只升级了libstdc，而我之前纠结的一直是gcc版本。

## 总结
问题最后圆满解决了，这个过程中也算是学到了不少东西，所以想记录一下。