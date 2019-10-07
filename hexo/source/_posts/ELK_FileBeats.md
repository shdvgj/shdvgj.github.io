---
title: 记一次ELK+FileBeats搭建
date: 2019-10-07 15:19:23
tags:
---
# 获取ElasticSearch+LogStash+Kibana+FileBeats  
关于elk搭建的全部产品都可以从elastic的官方网站获取最新版本  
[elastic官网](https://www.elastic.co/cn/products/)

![](ELK_FileBeats/1.png)
如图所示  
当前elastic官方已经不再推荐单纯使用elk(ElasticSearch+LogStash+Kibana)三个产品搭建日志平台, 而是在此基础上加上了轻量级的日志收集插件FileBeats.  

# 安装ElasticSearch  
我们首先获取安装包  
进入https://www.elastic.co/cn/downloads/elasticsearch  

![](https://user-gold-cdn.xitu.io/2019/10/7/16da3af23ba1aa25?w=617&h=97&f=png&s=2899)
在Downloads里点击linux就可以下载到用于linux安装的tar包  
如果是直接在linux安装,使用wget命令即可  
如 : wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.4.0-linux-x86_64.tar.gz  
如果需要更旧的历史版本, 点击下面的past release即可

![](https://user-gold-cdn.xitu.io/2019/10/7/16da3b1df131449b?w=603&h=336&f=png&s=9906)  

在获取到安装包之后, windows环境需要将tar包传送到linux , 使用scp(windows需要预装git)命令即可  
如: scp C:/Users/Administrator/Downloads/elasticsearch-7.3.1-linux-x86_64.tar.gz root@100.100.100.100:/home/elk/  
在linux, 进入到安装包的目录, 使用tar -zxvf命令进行解压  
如: tar -zxvf elasticsearch-7.3.1-linux-x86_64.tar.gz   
解压完成后, elasticsearch其实就已经完成了安装  
## 启动ElasticSearch  
在启动elasticserach之前需要注意:elasticsearch不可以用root用户启动, 因此, 需要事先在linux建立一个非root用户  
[linux如何创建一个管理员用户](https://www.cnblogs.com/sunyllove/p/9772053.html)
创建完linux用户后, 切换到该用户, 进入到elasticsearch目录, 执行bin/elasticserach即可
默认elasticsearch的端口是9200, 打开浏览器, 输入http://elasticsearch地址:9200/  , 出现如下画面, 说明elasticsearch启动成功

![](https://user-gold-cdn.xitu.io/2019/10/7/16da3c7f27d0b5b9?w=425&h=351&f=png&s=12567)

# 安装LogStash
windows环境下载和上传到linux的方法不再赘述, 参照前文  
linux环境, 参照如下命令即可:  
1.执行wget https://artifacts.elastic.co/downloads/logstash/logstash-7.4.0.tar.gz  
2.进入logstash安装包目录, 执行tar -zxvf logstash-7.4.0.tar.gz

## 配置和启动LogStash  
1.在启动logstash之前, 需要先新建一个logstash的配置文件  
参照logstash目录下的config/logstash-sample.config文件(如图), 新建一个logstash-elk.config文件  

![](https://user-gold-cdn.xitu.io/2019/10/7/16da3d7b60d5e808?w=601&h=226&f=png&s=5204)  
input, 即输入数据的监听端口, 这个一般不需要改.  
output, 用于将接收到的数据发送到elasitcsearch. 将hosts改成elasticsearch部署的地址, 如果elasticsearch和logstash是同一台机器, 则不需要更改.   
注意: 如果多个实例中的FileBeats发送过来的数据希望进入同一个elasticsearch实例, 并根据不同的ip地址划分为不同的索引, 按照如下方式配置  

```
output {
    if "100.100.100.101" in [host][ip] {
        elasticsearch{
          hosts => ["localhost:9200"]
          index => "sample1"
        }
    }else if "100.100.100.102" in [host][ip] {
        elasticsearch{
          hosts => ["localhost:9200"]
          index => "sample2"
        }
    } else {
      elasticsearch{
        hosts => ["localhost:9200"]
        index => "other"
      }
    }

}
```
配置完成后, 启动logstash, 执行bin/logstash -f config/logstash-elk.conf 

# 安装Kibana  
linux参照如下命令  
1.wget https://artifacts.elastic.co/downloads/kibana/kibana-7.4.0-linux-x86_64.tar.gz  
2.进入kibana安装包目录, 执行tar -zxvf kibana-7.4.0-linux-x86_64.tar.gz  

## 配置和启动kibana
进入config/kibana.yml  
1.修改server.host为linux机器的实际地址  
2.修改i18n.locale: "zh-CN" 这样kibana界面会显示为中文  
3.返回kibana主目录, 执行bin/kibana  (注意:kibana也不能用root用户启动)
4.打开浏览器, 输入http://kibana地址:5601, 出现如下画面

![](https://user-gold-cdn.xitu.io/2019/10/7/16da3ed853b4e3c3?w=1192&h=932&f=png&s=20153)

# 安装FileBeats  
注意: **FileBeats需要安装在所有需要发送日志的服务器上**  
linux参照如下命令  
1.wget https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-linux-x86_64.tar.gz
2.tar -zxvf filebeat-7.4.0-linux-x86_64.tar.gz

## 配置和启动FileBeats
进入filebeats安装目录, vi filebeat.yml
1.修改paths下的目录, 输入filebeats所在服务器需要收集的日志地址, 如:  

```
paths:
    - /home/sample1/logs/log.*
    - /home/sample2/logs/log.*
```  
2.修改output.logstash下的内容, 将logstash部署的地址输入, 参照如下:  

```
output.logstash:
  hosts: ["100.100.100.101:5044"]
  enabled: true
```
3.进入filebeats主目录, 执行 ./filebeat -e -c filebeat.yml, 启动成功  

# kibana查看日志内容
将上述Elk+FileBeats都安装搭建完成后, 浏览器进入到kibana的界面.  
1.打开管理-索引模式-创建索引模式, 进入如下界面:  

![](https://user-gold-cdn.xitu.io/2019/10/7/16da3fc725dee899?w=618&h=487&f=png&s=9440)  
2.可以看到有三个索引可以匹配, 在索引模式的输入框内输入自己想要创建的索引, 如other, 然后点击下一步

![](https://user-gold-cdn.xitu.io/2019/10/7/16da3fda72444141?w=1127&h=306&f=png&s=7460)
3.选择timestamp, 点击创建索引模式

![](https://user-gold-cdn.xitu.io/2019/10/7/16da3fe2cd04a5ad?w=1128&h=330&f=png&s=9376)
4.创建成功, 点击discover

![](https://user-gold-cdn.xitu.io/2019/10/7/16da3fefd464420b?w=106&h=568&f=png&s=6935)
5.可以看到有一个新的索引other可以选择

![](https://user-gold-cdn.xitu.io/2019/10/7/16da3fff382342f2?w=1854&h=861&f=png&s=62214)

6.到此, elk+filebeats的基本搭建工作就已经完成了