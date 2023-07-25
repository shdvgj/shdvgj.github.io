---
title: 每日文章收集-20230724
date: 2023-07-24 09:59:25
categories: 
- 每日文章收集
---

### [earth：可视化全球天气状况](https://github.com/cambecc/earth)

一个酷炫的开源项目，有一个在线的[demo](http://earth.nullschool.net)可以参观。确实非常可视化，可以看到在当前时间（2023年7月24日）在中国南部有一个台风。
![](earth1.png)

安装和使用：
```sh
# 安装
git clone https://github.com/cambecc/earth
cd earth
npm install
# 启动
node dev-server.js 8080
```

这样就可以通过访问`http://localhost:8080`看到项目的展示效果。这是个完全基于静态资源的项目，因此不需要任何额外的服务端。

地图数据来自[naturalearthdata](https://www.naturalearthdata.com/)，天气数据来自[Global Forecast System](http://en.wikipedia.org/wiki/Global_Forecast_System)。

### [weather-icons：天气图标](https://erikflowers.github.io/weather-icons/)

200多种天气图标，简洁美观。
![](weathericon1.png)

- 基本用法：将5个字体文件和主要的weather-icons.min.css文件放入你的项目中，假设这些字体文件位于你的CSS目录的上一级目录。

使用i元素来显示图标，并且首先添加基本类wi，然后再添加你想要的图标类，比如day-sunny。这看起来像是：`<i class="wi wi-day-sunny"></i>`。

- API用法：这套图标字体包含了流行的天气服务API的配套CSS文件。目前已经提供了Forecast.io、Open Weather Map、World Meteorological Organization、Weather Underground和Yahoo的映射。请查看API列表以查看类名。

### [Iconbuddy：超过10万个的开源图标库](https://iconbuddy.app/)

![](iconbuddy1.png)
几乎应有尽有，重要的还是开源，在项目中可以免费使用。

- 可以在线编辑图标的样式
![](iconbuddy2.png)
- 编辑完成后可以导出为svg，png，webp格式
![](iconbuddy3.png)
- 图标库支持直接搜索你要的图标
![](iconbuddy4.png)

### [提问的智慧](https://github.com/tvvocold/How-To-Ask-Questions-The-Smart-Way)

这是名著[《How To Ask Questions The Smart Way》](http://www.catb.org/~esr/faqs/smart-questions.html)的中文译本。特别需要每一个上网的网民去阅读，尤其是对于技术人员来说。简单罗列下目录，也差不多就是基本的原则：

- 仔细挑选论坛
- 面向新手的论坛和互联网中继聊天（IRC）通常响应最快
- 第二步，使用项目的邮件列表
- 使用有意义且明确的主题
- 使问题容易回复
- 用清晰、语法、拼写正确的语句书写
- 使用易于读取且标准的文件格式发送问题
- 描述问题应准确且有内容
- 量不在多，精炼则灵
- 别急于宣称找到Bug
- 低声下气代替不了做自己的家庭作业
- 描述问题症状而不是猜测
- 按时间先后罗列问题症状
- 描述目标而不是过程
- 别要求私下回复电邮
- 提问应明确
- 关于代码的问题
- 别张贴家庭作业式问题
- 删除无意义的要求
- 不要把问题标记为“紧急”，即使对你而言的确如此
- 礼貌总是有益的
- 问题解决后追加一条简要说明

以及两条能解决百分之99问题的原则：“读读该死的手册”（RTFM）和“搜搜该死的网络”（STFW）。不要总想着不劳而获。

### [健康的编程原则](https://puppycoding.com/2023/07/22/healthy-coding-habits/)

这篇文章不是告诉你如何让代码更有健壮性，一致性之类的，而是告诉你如何让你能够继续在热爱的编码行业中享受多几年乃至几十年。

- 日常拉伸
- 定时休息
- 不要在深夜编码
- 提升你的编码环境






