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

## 修改hostname

```shell
vi /etc/sysconfig/network
```

## 传输文件

```shell
scp C:/Users/Administrator/Downloads/elasticsearch-7.3.1-linux-x86_64.tar.gz root@192.168.15.161:/home/bos-provider/bos-provider-1.0-SNAPSHOT/bin

```
<!-- more -->
## MQ查询命令

#### 查询某条消息的生产者和消费者，以及消息的track路径

1.进入/usr/java/rocketmq/bin
2.根据key查找

```shell
sh mqadmin queryMsgBykey -n 127.0.0.1:9876 -k b6df2c64-4654-409d-b457-ec1db294a606 -t ORDER
```

3.根据key可以找到ID
   根据ID查找消费者和生产者

```shell
sh mqadmin queryMsgById -n 127.0.0.1:9876 -i C0A8074100002A9F00000001A2BC9012
```

#### 查看topiclist
```shell
sh mqadmin topicList –n 127.0.0.1:9876
```

### curl发送post请求

```jshelllanguage
date=`date +%Y-%m-%d `;curl -H "Content-Type: application/json" -X POST -d '{"dateBegin":"'$date' 00:00:00","dateEnd":"'$date' 23:59:59"}' "http://192.168.15.162:8082/hr/syncPunchRecordByUser"
```

参数 内容
-H 请求头
-d POST内容
-X 请求协议

