---
title: ELK日常维护相关
date: 2019-10-14 13:25:13
tags: 
- elk
comments: true
categories: 
- 学习
- 搭建相关
---
## 配置启动ELK+FileBeats后台运行, 不随着窗口关闭而关闭  
只要在后面加上&即可, 如  
```shell
bin/logstash -f config/log4j-es.conf &
```
注意: 退出时使用Ctrl+D命令, 直接关闭窗口依然有可能使得进程关闭  

## LogStash自动重新加载配置  
启动logstash时在后面加上命令--config.reload.automatic  
```shell
bin/logstash -f config/log4j-es.conf --config.reload.automatic &
```

## 重新发送filebeat抓取的内容  
1.删除registry下的data.json  
如:  
```shell
rm -rf /home/elk/filebeats/filebeat-7.3.1-linux-x86_64/data/registry/filebeat
```
2.重启filebeat  
如果存在正在运行的filebeat, 先kill掉  
```shell
ps aux | grep filebeat  
kill -s 9 pid
```
然后执行  
```shell
./filebeat -e -c filebeat.yml
```

## 设置索引内容30天过期删除  
本来我理解的理论上应该可以用索引的生命周期来实现, 但是半天没整明白, 所以现在
是简单粗暴的使用linux的cron任务来实现, 更具体的cron配置详见[cron详解](https://blog.csdn.net/mengzuchao/article/details/81172305)  
1.编辑shell脚本, 文件名为removeDataByTime.sh, 内容如下  
```shell
#!/bin/bash
limit_date=30
for var in `curl 'localhost:9200/_cat/indices' | awk '!/kibana*/ {print $3}'`
do
  curl -H "Content-Type:application/json" -X POST -d '{"query": {"range": {"@timestamp": {"lt": "now-'${limit_date}'d/d"}}}}' "http://localhost:9200/${var}/_delete_by_query"
done
```
2.编辑cron文件, 执行crantab -e, 打开crontab配置文件  
加上刚才的shell脚本, 设置每天0点15分执行  
```shell
15 00 * * * /home/elk/removeDataByTime.sh
```
设置cron重新启动, 执行/sbin/service crond restart  

3.查看cron执行情况  
查看cron任务列表 : crontab -l  
查看cron执行日志 : grep 'removeDataByTime' /var/log/cron  




  
  
