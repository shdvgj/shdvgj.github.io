---
title: 在mac端远程连接windows并使用mac快捷键
date: 2024-04-22 15:02:33
tags:
---

## 安装远程连接工具

这两个工具的安装就不多说了，就简单讲讲这两个工具的使用体验。

### 内网连接推荐使用Microsoft Remote Desktop

Microsoft Remote Desktop是官方工具，无论理论上还是实际使用来说都是最好的选择，有更好的体验。因此有条件的情况下都最好使用MRD。

### 外网推荐使用TeamViewer

TeamViewer只要有网就能用，而且跨平台，甚至ipad都能连，这点还是很强大的。我有段时间确实也用过ipad连接公司的TeamViewer来进行远程办公，对网速要求会比较高。

## 安装改键工具

### 推荐使用微软官方工具PowerToys

- 安装[https://learn.microsoft.com/en-us/windows/powertoys/](https://learn.microsoft.com/en-us/windows/powertoys/)
- 点击Keyboard Manager - Enable Keyboard Manager 【On】
- 点击Remap a key，实现单个键盘按键点击映射为另一个键，如command映射为ctrl
- 点击Remap a shortcut，实现复合快捷键的映射，如Ctrl+A映射为Ctrl+B

从实际体验来看，基本上把command映射为ctrl就够用了。至少对我来说不用Ctrl+C/V和Command+C/V两套不用换来换去，面向Google/ChatGPT编程是够用了。

**注意事项** 每次改完键后可能会无效，此时重启一次软件就可以了。

### 另外推荐安装Vim

真正实现一套short cut，四处使用。又有逼格又省心省力。
