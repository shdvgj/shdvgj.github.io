---
title: 每日文章收集-20230628
date: 2023-06-28 09:55:34
comments: true
categories: 
- 每日文章收集
---

# 【今日关注】

### [更快的机器，更慢的机器](https://jmmv.dev/2023/06/fast-machines-slow-machines.html)

这篇文章的作者在推特上发布了两段[视频](https://twitter.com/jmmv/status/1671670996921896960)，他用了一台装了windowsNT 3.51的旧电脑和一台装了windows11的新电脑，分别打开并关闭了windows的原生应用：命令行工具，资源管理器，记事本和画图。结果是，旧电脑没有任何延迟，新电脑却出现明显的加载延迟。这俩视频在推特上引起大量的关注和转发，大部分人都表示认同视频里的展示结果并发出了抱怨：为什么机器硬件越来越好，响应速度却越来越慢？

#### 第一版对比视频

作者的第一版视频对于两部机器的介绍是：

- 一台搭载AMD K7-600处理器、128MB内存和5400转/分钟的硬盘的计算机，运行着Windows NT 3.51操作系统。这是一台1999-2000年的机器，而其操作系统比它还要旧约5年左右。
![](fastslow1.png)
- 一台配备英特尔Core m3处理器、8GB内存和运行Windows 11的SSD的Surface Go 2。这是一台三年前发布的机器，原装搭载Windows 10，但官方支持升级至Windows 11。
![](fastslow2.png)

后来被人指出surface go的配置是错误的，因为作者错误的引用了Surface Laptop Go 2的配置。同时由于app已经预先打开过，所以都在内存中已经存在，这次的对比显然不够公平。

#### 第二版对比视频

作者重新发布了一版对比视频，这次的两台机器是：

- 在K7-600机器上安装Windows 2000。这是一个来自1999年的操作系统，在同一年的硬件上运行。
![](fastslow3.png)
- 在Mac Pro 2013上安装Windows 11，配备一颗3.5GHz的6核Xeon E5-1650v2处理器，32GB内存，双GPU以及一块最高可持续读取速度达到1GB/s的SSD。
![](fastslow4.png)

注意到第二台电脑是一台十年前的机器运行近几年的系统，但是作者认为这么一台十年前顶配的电脑在打开最简单的桌面应用时有延迟也是不可接受的。

两版视频揭示了同样的结果，更旧的电脑更旧的系统确实反应极快，打开这些桌面应用都是秒开，而更新的电脑和更新的系统却都同样有极其明显的延迟。

#### 计算机的进步

从计算机产业角度来看，在21世纪的计算机发生了巨大的技术进步：

- 更佳的图形和高性能显示器，更快的网络，实时视频剪辑等。
- 从难以忍受的缓慢I/O读取速度的HDD硬盘，CD和DVD进化到超高速的SSD，使得计算机速度，大型游戏运行，一次性打开超大图片的文件夹都变得顺畅无比。
- 硬件更易安装，无线设备普及，文本的国际化（unicode的进步）。

#### 潜在的隐患

经历了以上巨大进步的计算机产业，为啥还会出现作者一开始展示的视频的情况呢？有人提出可能是更新的系统需要加载更多的像素，为了展示更好的图形。这显然不是一个很好的理由，作者也马上发了一个打脸视频，将新机器的像素调到最低，关闭4k，关闭特效等等图形效果，结果打开应用的延迟并没有任何改善。
![](fastslow5.png)

GPU的出现减轻了CPU的负担，而出现在图形上的延迟通常是由于动画的有意暂停导致，为了兼容4k显示器在老旧机器上运行。但是作者发现的延迟并不属于这种情况。

#### 一些别的实例

- windows上的记事本经历了无数代都还能响应快速，直到UWP的出现，情况开始急转直下。
- windows的命令行工具随着越来越多的可视化特性，变得越来越慢，PowerShell的新窗口打开甚至要花上几秒钟。
- macos也没有表现的更好，甚至M1芯片的电脑也在打开系统设置时明显慢于以前的机器。
![](fastslow6.png)
- linux看起来受到影响较小，原生的应用即使在10多年前的电脑上也能运行出色。然而一旦你开始安装第三方的程序，就会开始感受到变慢。
- 谷歌的程序也是，最早的原生谷歌应用非常快速，现在也开始表现不佳。

#### 可能的原因

- 会是软件臃肿的原因吗？根据帕累托法则，80%的用户只用到了20%的软件功能，但是根据剩下20%的用户行为却又各自不同。
- 为了解决软件臃肿的问题，我们引入了框架和抽象。但是看看RUST就知道，这并不是问题。
- 大型程序和软件的开发需要优先考虑的是节省开发者的时间，这意味着更高的优先级是花大量的时间调试程序而非优化响应时间。
- 多平台的开发导致开发者想要节省时间，于是考虑跨平台框架，例如Electron。
- 这种跨平台的框架显然导致了很多程序的延迟，比如1password和spotify。
- 另一个可能的原因是解释性语言的盛行，例如java和.NET，这些语言在优化长期运行的进程时表现优秀，但是并不会怎么去优先考虑快速启动。

#### 逐渐消失的一次性改进

这里说的是从HDD到SSD的巨大改进，这个改进直接将电脑的表现提升到了另一个层次。但是随着时间发展和软件开发的日渐臃肿，这个一次性的改进优势逐渐被蚕食。现在你已经无法感受到SSD刚刚出现那会儿的极速体验了。而另一方面，现在操作系统几乎已经没有办法运行在HDD硬盘上了，当代的操作系统已经默认了你使用SSD，以此为基础，软件程序才能获得基本的体验。

同样的事情可能也会发生在苹果芯片上，缺乏节制的软件设计，完全不去节约系统资源的开发模式，也会逐渐蚕食掉苹果芯片的硬件改进，多年以后，软件速度也会渐渐慢下来。

> 计算机架构师能否通过其他革命性的技术转变来拯救我们呢？我不希望依赖这一点。不是因为这些转变可能不存在，而是因为我们不应该需要它们。

# 【博文一览】

### [密码游戏](https://neal.fun/password-game/)

颇为有意思的一个网页游戏，游戏界面很简单，只要输入符合规则的密码就行，一开始的规则非常正常，到后面的脑洞就越来越大。

- 一开始还是很正常的，至少5个字符，包含特殊符号和大写字母等常用的密码验证规则。
![](passwordgame1.png)
- 渐渐画风开始变得奇怪，要包含赞助商的名字。
![](passwordgame2.png)
- 渐渐离谱，要包含谷歌街景里这个国家的名字。
![](passwordgame3.png)
- 开始脑筋急转弯，把这个蛋安全的放到你的密码里。
![](passwordgame4.png)

这个游戏更适合不懂得前端技术的人来玩，因为这些验证规则完全在前端实现，技术人员通过浏览器的开发者工具就可以轻松实现作弊。例如上面的谷歌街景国家问题，直接可以从js里面找到答案是norway。
![](passwordgame5.png)

当然，对于前端人员来说，这个网页也颇有学习的价值，这些五花八门的验证规则细想起来也不是那么简单就能通过js来实现的，看这个网站的js源码非常具有借鉴价值。

### [进阶MacOS命令行工具](https://saurabhs.org/advanced-macos-commands)

我们知道在linux上的命令绝大部分都能在macos上使用，这里介绍几个在macos上的专属命令，不用去找复杂的第三方工具，可以实现很多常用的功能。

- `caffeinate` 设定mac睡眠行为
- `pbcopy, pbpaste` 与系统剪贴板互动
- `networkQuality` 测算网速
- `sips` 图片操作
- `open` 打开文件或者应用
- `textutil` 文档转换器（主要用于html和doc文档的互转）
- `mdfind, mdls` 聚光灯搜索
- `screencapture` 屏幕截图
- `taskpolicy` 控制进程调度
- `say` 文本阅读
- `pmset` 配置电源管理
- `networksetup` 配置网络设定
- `qlmanage` 管理快速查看
- `softwareupdate` 管理系统升级
- `system_profiler` 查看系统信息

### [用Linux的Namespaces，cgroups和chroot搭建你自己docker](https://akashrajpurohit.com/blog/build-your-own-docker-with-linux-namespaces-cgroups-and-chroot-handson-guide/)

docker是总所周知的容器平台，这篇文章不是旨在取代docker，而是通过这么一篇介绍让读者明白Docker中容器的核心机制。

首先介绍linux的这几个概念：

- [Namespaces](https://akashrajpurohit.com/blog/linux-namespaces-isolating-your-system-for-enhanced-security-and-performance/)：命名空间（namespaces）是一种内核特性，用于将系统资源隔离和虚拟化，使得每个进程拥有独立的视图，以便在不同的命名空间中运行。
- [cgroups](https://akashrajpurohit.com/blog/linux-control-groups-finetuning-resource-allocation-for-optimal-system-performance/)：控制组（cgroups）是一种资源管理机制，用于对进程和进程组进行资源限制和分配。
- [chroot](https://akashrajpurohit.com/blog/how-to-create-a-restricted-environment-with-the-linux-chroot-command/)：chroot是一种操作系统级别的功能，用于将进程的根目录更改为指定的目录，创建一个被隔离的运行环境。

搭建“docker”

- 使用`unshare`命令指定不同的命名空间。
```sh
unshare --uts --pid --net --mount --ipc --fork
```
- 执行如下命令创建一个新的cgroup，并分配cpu的配额限制。
```sh
## 创建新的cgroup
mkdir /sys/fs/cgroup/cpu/container1
## 分配cpu限额
echo 100000 > /sys/fs/cgroup/cpu/container1/cpu.cfs_quota_us
## 写入0用于移除之前曾分配过的进程
echo 0 > /sys/fs/cgroup/cpu/container1/tasks
## 写入$$ 确保当前进程的shell后续进程会成为croup中的子进程，并收到cpu限额的限制
echo $$ > /sys/fs/cgroup/cpu/container1/tasks
```
- 使用`debootstrap`命令创建容器的根文件目录。
```sh
debootstrap focal ./ubuntu-rootfs http://archive.ubuntu.com/ubuntu/
```
- 挂载系统目录和chroot容器
```sh
## 挂载proc文件目录，proc文件提供关于进程和系统资源的信息。
mount -t proc none ./ubuntu-rootfs/proc
## 挂载sysfs文件目录，sysfs文件系统以分层格式提供有关设备、驱动程序和其他与内核相关的信息。
mount -t sysfs none ./ubuntu-rootfs/sys
## 绑定dev目录，dev目录目录包含表示系统上物理和虚拟设备的设备文件。
mount -o bind /dev ./ubuntu-rootfs/dev
## 
chroot ./ubuntu-rootfs /bin/bash
```
- 完成上面几步后，就搭建了自己的容器环境，现在可以在容器内运行程序
```sh
(container) $ apt update
(container) $ apt install nginx
(container) $ service nginx start
```

## [OPNsense：开源的安全平台](https://opnsense.org/)

![](opnsense1.png)
根据官网介绍，涉及安全方面的内容，从虚拟私人网络到入侵检测，这是一款一流的免费开源项目。有下列功能（翻译自chatgpt）：

- 状态防火墙：具有对IPv4和IPv6的支持以及对被阻止或通过的流量的实时查看。
- 多WAN：具备多WAN功能，包括负载均衡和故障转移支持。
- 虚拟私人网络：集成支持IPsec（包括基于路由的IPsec）、OpenVPN，以及可插拔的支持Tinc（全网VPN）和WireGuard。
- 硬件故障转移：当您不能承受停机时间时，请使用我们的自动无缝硬件故障转移功能，利用常见地址冗余协议（CARP）进行状态同步，以实现最高可能的可用性。
- 软件定义广域网（SD-WAN）：为了轻松进行设置、配置和监控，可以使用ZeroTier插件在几分钟内设置您的软件定义广域网（SD-WAN）。
- 入侵检测和防御：利用Suricata和Proofpoint的新兴威胁开放规则，采用最先进的内联入侵预防技术，摆脱木马和CNC机器人的困扰。可选择集成ET PRO（商业订阅）或ET PRO Telemetry（免费注册）。
- 两步验证（2FA）：系统中始终支持两步验证（2FA），包括用户界面和VPN等服务。
- 路由协议：使用Free Range Router项目，提供可插拔的OSPF和BGP支持。
- 网络过滤：完全集成的Web代理，具有访问控制和支持外部黑名单，以过滤不需要的流量。其他选项包括防火墙别名和DNS黑名单。轻松屏蔽广告！
- 直观的用户界面：在任何开源防火墙中都可以找到的最直观、完全响应的用户界面，具有集成搜索选项。
- 多语言：用户可选择的语言支持，包括英语、捷克语、中文、法语、德语、意大利语、日语、葡萄牙语、俄语和西班牙语。
- 在线文档：完全免费并支持搜索的在线文档。

## [Mofi：免费且无需下载的音乐编辑网站](https://mofi.loud.red/)

- 将音乐文件拖拽到该网站，或者直接粘贴音乐的网址，就可以开始进行编辑。
![](mofi1.png)
- 进入到编辑界面，上面部分是整首歌的音轨，下面部分是根据你的编辑生成的cut，可以进行导出。
![](mofi2.png)
- 选择需要编辑的部分，并选择Avoid this，Prefer this，Repeat this进行编辑。网站会根据你的选择生成匹配的cut。
![](mofi3.png)