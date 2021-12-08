---
title: 用curl计算网络请求的时间
date: 2021-11-29 09:51:08
tags: 
- linux
- translate
comments: true
categories: 
- 学习
---
**声明**

翻译自[Timing With Curl](https://susam.in/maze/timing-with-curl.html)

---

当一个http请求花费了太长时间的时候，我经常用下面的命令来查找原因。
```shell
curl -L -w "time_namelookup: %{time_namelookup}
time_connect: %{time_connect}
time_appconnect: %{time_appconnect}
time_pretransfer: %{time_pretransfer}
time_redirect: %{time_redirect}
time_starttransfer: %{time_starttransfer}
time_total: %{time_total}
" https://example.com/
```
下面是用一条指令来实现的同样的命令，我可以在未来用到的时候，在这个页面通过三次点击快速复制。
```shell
curl -L -w "time_namelookup: %{time_namelookup}\ntime_connect: %{time_connect}\ntime_appconnect: %{time_appconnect}\ntime_pretransfer: %{time_pretransfer}\ntime_redirect: %{time_redirect}\ntime_starttransfer: %{time_starttransfer}\ntime_total: %{time_total}\n" https://example.com/
```
这个命令输入之后，一般能看到以下输出：
```shell
$ curl -L -w "namelookup: %{time_namelookup}\nconnect: %{time_connect}\nappconnect: %{time_appconnect}\npretransfer: %{time_pretransfer}\nstarttransfer: %{time_starttransfer}\ntotal: %{time_total}\n" https://example.com/
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
...
</html>
time_namelookup: 0.001403
time_connect: 0.245464
time_appconnect: 0.757656
time_pretransfer: 0.757823
time_redirect: 0.000000
time_starttransfer: 0.982111
time_total: 0.982326
```
为了简洁起见，在上面的输出中，我用省略号忽略了大部分的html部分的输出。
下面的列表描述了输出中每一个数字的含义。这些信息直接取自curl 7.20.0版本的手册。以下是详情：

- *time_namelookup*: 以秒为单位，表示从命令开始到域名被解析完成所需时间。
- *time_connect*: 以秒为单位，表示从命令开始到TCP请求完成连接到远程端口（或代理）所需时间。
- *time_appconnect*: 以秒为单位，表示从命令开始到SSL／SSH等请求完成到远程端口的连接／握手所需时间。（7.19.0版本后加入）
- *time_pretransfer*: 以秒为单位，表示从命令开始到文件传输开始所需时间。这包括所有的预传输命令和协议。包含所有涉及到特定协议的预传输指令和谈判（？）。
- *time_redirect*: 以秒为单位，包括所有的重定向步骤，从域名检索，连接，预传输以及在最后交换数据开始之前的传输所需时间。time_redirect展示了多个重定向请求的完整执行时间。（7.12.3版本后加入）
- *time_starttransfer*: 以秒为单位，表示从命令开始到第一个字节被传输所属时间。这包含了*time_pretransfer*，以及服务器计算结果所属的时间。
- *time_total*: 以秒为单位，表示整个操作持续到最后所需的总时间。这个时间会精确到毫秒进行展现。

一个没啥用的关键细节是，*time_appconnect*和*time_connect*之间的时间差可以告诉我们SSL／TLS握手所需时间。对于一个没有SSL／TLS的干净连接，这个时间会显示为0。下面的输出证明了这件事：
```shell
$ curl -L -w "time_namelookup: %{time_namelookup}\ntime_connect: %{time_connect}\ntime_appconnect: %{time_appconnect}\ntime_pretransfer: %{time_pretransfer}\ntime_redirect: %{time_redirect}\ntime_starttransfer: %{time_starttransfer}\ntime_total: %{time_total}\n" http://example.com/
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN">
<html>
...
</html>
time_namelookup: 0.001507
time_connect: 0.247032
time_appconnect: 0.000000
time_pretransfer: 0.247122
time_redirect: 0.000000
time_starttransfer: 0.512645
time_total: 0.512853
```

同样注意*time_redirect*在上面的输出中也是0。这是因为访问example.com的时候，没有重定向发生。以下是另一个例子，展示了当重定向发生时输出会是什么样子：
```shell
$ curl -L -w "time_namelookup: %{time_namelookup}\ntime_connect: %{time_connect}\ntime_appconnect: %{time_appconnect}\ntime_pretransfer: %{time_pretransfer}\ntime_redirect: %{time_redirect}\ntime_starttransfer: %{time_starttransfer}\ntime_total: %{time_total}\n" https://susam.in/blog
<!DOCTYPE HTML>
<html>
...
</html>
time_namelookup: 0.001886
time_connect: 0.152445
time_appconnect: 0.465326
time_pretransfer: 0.465413
time_redirect: 0.614289
time_starttransfer: 0.763997
time_total: 0.765413
```

当网络服务遭遇一个潜在的延迟事件时，我会在多个客户端优先使用这条命令，因为这个命令返回的结果帮助我快速找到网络延迟的原因。
