---
title: jenkins简单搭建
date: 2020-05-14 13:37:55
tags:
- jenkins
categories:
- 学习
- 搭建相关
---
## 下载和安装jenkins
相对简单的办法，依次执行以下语句(前提，需要先安装rpm) 
```shell
#获取jenkins的rpm安装文件
wget https://pkg.jenkins.io/redhat-stable/jenkins-2.222.3-1.1.noarch.rpm
#使用rpm安装
rpm -ivh jenkins-2.222.3-1.1.noarch.rpm 
```

## 启动jenkins 
jenkins启动需要先安装java，java的安装不再赘述，java安装完成后，修改jenkins配置文件，指向java的安装目录 
```shell
vi /etc/init.d/jenkins
```
在"candidates="这一行加入java安装目录，如果默认配置里面已经指向了你的java安装目录，则不需要再次配置。
```shell
candidates="
/etc/alternatives/java
/usr/lib/jvm/java-1.8.0/bin/java
/usr/lib/jvm/jre-1.8.0/bin/java
/usr/lib/jvm/java-1.7.0/bin/java
/usr/lib/jvm/jre-1.7.0/bin/java
/usr/lib/jvm/java-11.0/bin/java
/usr/lib/jvm/jre-11.0/bin/java
/usr/lib/jvm/java-11-openjdk-amd64
/usr/bin/java
/usr/java/jdk1.8.0_121/jre/bin/java
"
``` 
<!-- more -->
修改jenkins端口 
```shell
vi /etc/sysconfig/jenkins
# JENKINS_PORT="8999"
``` 

配置完成后，执行：
```shell
service jenkins start
```
执行
```shell
service jenkins status
``` 
出现以下截图说明成功启动
![](1.png)  

## 配置jenkins
访问 http://localhost:8999/ 即可看到jenkins的启动页面，稍等一会儿，看到如下页面：
![](2.png)  
接下来按照页面指引一步步安装即可。
- 需要注意的点：插件按照推荐插件安装后，可能会有个别插件无法完成安装，这时不要选择重试，直接跳过，在Manage Jenkins - Mange Plugins - 高级，修改URL为 http://mirror.esuni.jp/jenkins/updates/update-center.json ，然后再更新插件即可。  

