---
title: linux日常维护命令
date: 2019-10-16 14:32:27
tags: 
- linux
comments: true
categories: 
- 学习
- 运维
---

## find  
### 删除测试库创建日期在10天以前的日志  
```shell
find /home -ctime +10 -type f -name "*log.log.20*" | xargs rm -rf
```

## 查看磁盘空间和大文件  

### 查看磁盘剩余空间  

```shell
df -h
```

### 查看目录下大文件  

``` shell
du -h --max-depth=1
```

## 查看进程中的java程序  

```shell
ps aux | grep java
# 杀死某个进程
kill -9 pid
```

