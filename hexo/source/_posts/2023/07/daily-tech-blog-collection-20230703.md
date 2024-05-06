---
title: 每日文章收集-20230703
date: 2023-07-03 12:39:25
categories: 
- 每日文章收集
---

# 【今日关注】

### [用iPad编程是一个错误](https://technicallychallenged.substack.com/p/buying-an-ipad-pro-for-coding-was)

作者买了个M1芯片的iPad，寄希望于可以实现轻量级的编程。

#### 为什么要选择iPad？

- 首先是为了能够阅读编码类的书籍，这方面，kindle几乎没法阅读代码书籍，用电脑看太大，体验不佳，只有iPad是完美的选择。
- 另一方面，苹果在2021年的WWDC上发布了在iPad上可用的Swift编码程序。
![](ipad1.png)

#### iPad在哪方面表现优秀？

- 很多很多。写作，制表，音乐制作，视频剪辑。
- 可以用笔的操作也很棒：笔记，绘画，照片编辑，以及用Final Cut Pro编辑你的视频。

#### 配件准备

- 由于想要编程，所以作者买了magic keyboard。ipad搭配magic keyboard可以拥有mac上80%的体验。
- 搭配了鼠标，iPad上新增的鼠标指针功能在使用了一段时间后，发现它也非常好用。
- 第二代ipad pencil非常棒，可靠而精确。

<!-- more -->

#### 性能和电池

- 新的M1芯片表现卓越，性能和电池续航都很出色。
- 电池续航尤其值得一提，据作者说，可以数天不充电依然电量充足。

#### 重量

iPad本身非常轻巧，但是一旦增加了上述配件，iPad就会增加到几乎两倍重量。权衡利弊，作者认为这是值得的。

#### 你能够在iPad上编程吗？

简短回答：可以。

详细回答：这实际上取决于你想要进行什么类型的编码以及你将使用何种开发环境。如果你只是在Swift Playgrounds中学习Swift，那么当然可以用它来编码，但是你也可以使用基本款的iPad以节省成本。

如果你是一名主要在SSH终端中工作的系统管理员，或者如果你可以访问基于浏览器的云开发环境，那么iPad适合你的工作流程，尤其是如果你想将它与外接显示器或USB-C转HDMI一起使用。

但是，如果你需要运行Xcode、VSCode、WebStorm或Intellij，iPad则不适用。

#### 为什么不应该在2023年买一个iPad

- 它很昂贵。购买了一款功能强大的专业版iPad、一款不错的键盘和一支笔后，价格比本可以购买的用于更多编码活动的笔记本电脑还要高。
- 键盘快捷键和操作系统不太适合高级用户的需求。某些应用程序中的一些快捷键无法工作，这取决于开发者编码应用程序的方式和其支持的功能。而且其他类型的自定义选项也不够丰富，无法满足需求。
- 屏幕较小 - 为了节省一些成本，作者选择了11英寸而不是12.9英寸的版本。即使选择了12.9英寸，仍然太小了。我最舒适的是15或16英寸的笔记本电脑，所以11英寸对我来说只是微不足道的。但要承认的是，你可以连接外接显示器来扩大屏幕。

**总之，目前看来，用iPad编程不如买一个相对便宜的MacBook。**

# 【博文一览】

### [推特在DDOS攻击自己](https://sfba.social/@sysop408/110639435788921057)

博主通过后台发现推特在ddos攻击推特自己，这件事本身就很有趣，而细究此事的前因后果就更具有戏剧性。

- 6月30号，推特老总埃隆马斯克新出了一项[浏览规则](https://techcrunch.com/2023/06/30/twitter-now-requires-an-account-to-view-tweets/)，即推特内容不再像以前那样可以不登录就能浏览，现在要求用户必须注册并登录。
![](twitter1.png)
- 这件事当然引起轩然大波，这意味着推特将自己的内容封闭了，不再有公共内容。
- 7月1号开始有用户发现推特的内容无法加载，整个服务似乎已经挂了。
![](twitter2.png)
- 7月2号也就是这篇文章的作者发现推特以每秒10次的频率在请求推特的服务器拉取内容，而这个bug的原因竟然就是必须登录才能浏览推特的这个规定：即不登录的情况下浏览器拉取不到内容，于是脚本不断的请求服务器。可想而知，全球大量的不登录请求直接干趴了推特服务器。
- 而马斯克还在继续作妖，于7月2号又发布了一篇惊世骇俗的推文，规定了推特的浏览限制：认证用户可以一天浏览6000条推文，非认证用户600条，新注册的非认证用户只能浏览300条。
![](twitter3.png)

难以想象在如今这个流量为王的互联网时代，马斯克居然推出了这么一系列反其道而行之的规定，无论乍看还是细想，都觉得这是把推特推上死路。但是考虑到马斯克这个人有多爱整活，又让人非常想要知道他这么做的背后逻辑。至于后续如何，只能继续拭目以待了。
![](twitter4.jpeg)

### [Far Cry 源码公布](https://archive.org/details/far-cry-1.34-complete)

![](farcry1.png)
Far Cry是著名的游戏系列，是标准的3A大作，最近有网友发现在知名免费资源网站(archive.org)上居然看到了Far Cry的完整源码上传。

虽然这段源码只是2004年的第一部Far Cry，由古早引擎开发的，但是对于网友来说，这依然是非常宝贵的游戏开发学习资料。

厂商的这个慈善行为（没有明确是厂商上传，但我想应该也没别人敢干这事儿）得到了大量网友的点赞。
![](farcry2.png)

### [美国FTC：虚假的评论内容将会违法并承受重罚](https://www.washingtonpost.com/technology/2023/06/30/fake-reviews-online-ftc/)

联邦贸易委员会（The Federal Trade Commission）将对网络评论的贸易行为进行控制，如果新的规定通过，对于虚假评论的处罚将非常严重，每次虚假评论被消费者看到都将遭到最多50000美元的处罚，注意，**是每次被看到都遭到50000美元处罚**。

据统计30%到40%的评论都是捏造的（这个应该是美国的比例），尤其在chatgpt这样的人工智能出现后，虚假的但是随机而富有逻辑的正面评论将会弥漫于网络，让消费者难以发现客观正确的产品评价，FTC正是对于这样的情况做出这样顶格处罚的规定。

具体违反规定的评论包括：
- 一些歪曲实际产品体验的评论
- 由不存在的用户提供的评论
- 由内部人员提供的评论
- 由中间人生成的评论
- 劫持别的用户账号进行评论

另外一些情况不违反规定：厂商要求真实用户提供评论或者厂商提供有偿奖励真实用户评论。

另外新规也不会要求网站去负责这些虚假评论，除非他们直接参与了这些虚假评论的产生。

一些大厂对于新规的回应颇为积极：亚马逊声称在2022年屏蔽了超过20亿条虚假评论，yelp声称2022年将19%的评论标记为“不推荐”。

### [Gping：图形化ping](https://github.com/orf/gping)

这个开源项目非常好理解，就是将ping命令进行图形化，具体效果如下：
![](gping1.png)

#### 安装方式：
- macOS
    - Homebrew: `brew install gping`
    - MacPorts: `sudo port install gping`
- Linux (Homebrew): `brew install gping`
- CentOS (或者任何搭载老版本glibc的发行版): 下载MUSL并编译最新版本
- Windows/ARM:
    - Scoop: `scoop install gping`
    - Chocolatey: `choco install gping`
    - 从github发行页面下载最新版本
- Fedora (COPR): `sudo dnf copr enable atim/gping -y && sudo dnf install gping`
- Cargo (需要rustc版本1.44.0以上): `cargo install gping`
- Arch Linux: `pacman -S gping`
- Ubuntu/Debian (Azlux's repo):
```sh
echo "deb http://packages.azlux.fr/debian/ buster main" | sudo tee /etc/apt/sources.list.d/azlux.list
wget -qO - https://azlux.fr/repo.gpg.key | sudo apt-key add -
sudo apt update
sudo apt install gping
```
- Gentoo (dm9pZCAq overlay):
```sh
sudo eselect repository enable dm9pZCAq
sudo emerge --sync dm9pZCAq
sudo emerge net-misc/gping::dm9pZCAq
```
- FreeBSD:
```sh
pkg: pkg install gping
ports cd /usr/ports/net-mgmt/gping; make install clean
```

#### 用法
没啥特别的，ping怎么用，gping就怎么用，最简单的用法`gping [host]`。
```sh
$ gping --help
Ping, but with a graph.

Usage: gping [OPTIONS] [HOSTS_OR_COMMANDS]...

Arguments:
  [HOSTS_OR_COMMANDS]...  Hosts or IPs to ping, or commands to run if --cmd is provided. Can use cloud shorthands like aws:eu-west-1.

Options:
      --cmd
          Graph the execution time for a list of commands rather than pinging hosts
  -n, --watch-interval <WATCH_INTERVAL>
          Watch interval seconds (provide partial seconds like '0.5'). Default for ping is 0.2, default for cmd is 0.5.
  -b, --buffer <BUFFER>
          Determines the number of seconds to display in the graph. [default: 30]
  -4
          Resolve ping targets to IPv4 address
  -6
          Resolve ping targets to IPv6 address
  -i, --interface <INTERFACE>
          Interface to use when pinging
  -s, --simple-graphics
          Uses dot characters instead of braille
      --vertical-margin <VERTICAL_MARGIN>
          Vertical margin around the graph (top and bottom) [default: 1]
      --horizontal-margin <HORIZONTAL_MARGIN>
          Horizontal margin around the graph (left and right) [default: 0]
  -c, --color <color>
          Assign color to a graph entry. This option can be defined more than once as a comma separated string, and the order which the colors are provided will be matched against the hosts or commands passed to gping. Hexadecimal RGB color codes are accepted in the form of '#RRGGBB' or the following color names: 'black', 'red', 'green', 'yellow', 'blue', 'magenta','cyan', 'gray', 'dark-gray', 'light-red', 'light-green', 'light-yellow', 'light-blue', 'light-magenta', 'light-cyan', and 'white'
  -h, --help
          Print help information
  -V, --version
          Print version information
      --clear
          Clear the graph from the terminal after closing the program
```

### [油管开始对广告屏蔽插件采取更加激进的手段](https://www.androidpolice.com/youtube-ad-blockers-three-strikes/)

有Reddit用户发现，youtube给他弹出了一个界面，告知要么将youtube加到广告屏蔽插件的白名单里，或者购买他们的YouTube Premium会员。
![](youtube1.jpeg)

youtube似乎开响了和广告屏蔽插件的第一枪，逼迫用户在他们的产品和广告屏蔽插件之间二选一。当然，市面上大部分网站和产品应该是没有这样的底气的，也就是youtube做到了这样的体量，用户粘性高，才敢这么激进的要求用户必须二选一。

目前这种弹窗还是在少部分用户中试行，而且知名广告屏蔽插件uBlock Origin也没出现这种情况，现在应该还只是youtube的一个小范围实验功能。一旦这个功能推广，那么你就只能要么永远忍受30s的视频前广告，或者订阅12美元一个月的会员服务。