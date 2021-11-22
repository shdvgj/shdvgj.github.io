---
title: azkaban简单搭建
date: 2020-04-29 09:15:11
tags:
- azkaban
categories:
- 学习
- 搭建相关
---
前言：
azkaban的用处和好处不再赘述，这里只讲怎么将azkaban在项目中使用起来。
另外非常推荐直接看[官方的英文文档](https://azkaban.readthedocs.io/en/latest/)进行学习，那将是最新最可靠的教程。
## 下载和安装azkaban  
### 下载azkaban  
方法1：直接到azkaban的github网页下载tar.gz包。截止2020.4.29日最新版本的包地址为(https://github.com/azkaban/azkaban/archive/3.84.10.tar.gz)  
方法2：在linux执行   
```shell
wget https://github.com/azkaban/azkaban/archive/3.84.10.tar.gz
```
### 安装azkaban  
将包进行解压，执行
```shell
tar -zxvf azkaban-3.81.10.tar.gz
```
解压完成后目录结构如下图  
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2020/04/29/azkaban简单搭建/1.png)  
如果使用solo-server方式的话，不需要建表，下面的步骤也都可以不用看，直接跳转看[solo-server](https://azkaban.readthedocs.io/en/latest/getStarted.html#getting-started-with-the-solo-server)  
但是个人理解solo-server只适用于小型简单的项目，如果考虑项目以后的扩充和发展的话，建议一开始就使用multi-executor-server方式，该方式需要建表。  
步骤如下：
### 新增azkaban相关表  
复制如下文件内的sql语句，在事先建好的数据库内执行  
```shell
vi azkaban-3.81.10/azkaban-db/build/sql/create-all-sql-0.1.0-SNAPSHOT.sql
```
- 可能有的坑：
早期版本的mysql可能不支持长度过长的字段设为索引和主键，遇到这种报错时，需要将对应的字段改短再执行。过长的字段比如 execution_logs.name，sql给设置的长度是varchar(640)，在我的mysql数据库中执行会报错，我改成了varchar(200)，这个字段的含义是任务名，一般来说你正常命名的话完全用不到640个字段的长度，所以可以放心修改。  
### 编译azkaban  
在azkaban目录下执行  
```
./gradlew build installDist
```
可能有的坑:
- 部分依赖包可能无法下载，修改build.gradle，将repositories指向国内阿里云地址，如下：
```
buildscript {
  repositories {
        maven {
            url 'http://maven.aliyun.com/nexus/content/groups/public/'
        }
        maven {
            url 'http://maven.aliyun.com/repository/google'
        }
        maven {
            url 'http://maven.aliyun.com/repository/jcenter'
        }
  }
  dependencies {
    classpath 'com.cinnober.gradle:semver-git:2.2.3'
    classpath 'net.ltgt.gradle:gradle-errorprone-plugin:0.0.14'
    classpath  'com.github.jengelman.gradle.plugins:shadow:4.0.0'
  }
}
```

- 如果出现编译报错的情况，可以尝试将编译期间的test过程注释掉，或者进入azkaban-executor-server和azkban-web-server目录下单独编译这两个包。  

### 启动azkban-executor-server  
- 修改配置文件 
```
vi ../azkaban-3.81.0/azkaban-exec-server/build/install/azkaban-exec-server/conf/azkaban.properties
```
需要修改的内容如下  
> default.timezone.id=Asia/Shanghai # 修改为中国时区
azkaban.webserver.url=localhost:8199 # 指向本地的azkaban-web-server地址
#数据库配置，指向azkban建表的数据库，根据实际的数据库配置来
database.type=mysql
mysql.port=3306
mysql.host=110.110.110.110
mysql.database=azkaban
mysql.user=azkaban
mysql.password=azkaban  

- 启动azkaban-executor-server  
```shell
cd ../azkaban-3.81.0/azkaban-exec-server/build/install/azkaban-exec-server;
bin/start-exec.sh; # 注意一定要按照这个语句执行,不要在bin目录下执行./start-exe.sh，否则某些资源文件会加载不到，导致报错
```
-  激活executor
```
# 指向executor地址和端口
curl localhost:port/executor?action=activate
```

### 启动azkaban-web-server  
- 修改配置文件
```
vi ../azkaban-3.81.0/azkaban-web-server/build/install/azkaban-exec-server/conf/azkaban.properties
```
需要修改的内容如下  
> default.timezone.id=Asia/Shanghai # 修改为中国时区
#邮件配置
mail.sender=888888@qq.com # 发送邮件的邮箱
mail.host=smtp.qq.com  # 邮件服务器
mail.user=888888@qq.com # 邮件用户
mail.password=abc123456 #密码需要取qq邮箱中获取到的授权码
job.failure.email=8888888@qq.com # 定时任务执行失败发送邮件的邮箱
#数据库配置，指向azkban建表的数据库，根据实际的数据库配置来
database.type=mysql
mysql.port=3306
mysql.host=110.110.110.110
mysql.database=azkaban
mysql.user=azkaban
mysql.password=azkaban

- 启动azkaban-web-server  
```shell
cd ../azkaban-3.81.0/azkaban-web-server/build/install/azkaban-web-server;
bin/start-web.sh;
```
### 访问azkaban  
浏览器输入 localhost:8199，出现如下界面 
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2020/04/29/azkaban简单搭建/2.png)  
用户名密码默认均为azkban，可以在 ../azkaban-3.81.0/azkaban-web-server/build/install/azkaban-web-server/conf/azkaban-users.xml 修改用户和密码  

## 配置定时任务  
