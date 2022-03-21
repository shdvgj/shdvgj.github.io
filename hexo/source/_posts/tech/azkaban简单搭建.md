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
<!-- more -->
![](1.png)  
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
![](2.png)  
用户名密码默认均为azkban，可以在 ../azkaban-3.81.0/azkaban-web-server/build/install/azkaban-web-server/conf/azkaban-users.xml 修改用户和密码  

## 配置定时任务  
1.打开azkaban主页面，点击create project，在弹出页面输入项目名和描述
![](3.png)
2.随后点击项目名进入配置页面，azkaban目前无法在页面直接配置，需要通过上传和下载配置文件来完成对定时任务的增删改，也就是用到页面上的upload和download按钮
![](4.png)
3.在本地新建一个test.flow文件，azkaban的基本配置文件需要用flow后缀的文件进行配置，配置的基本格式如下
```properties

---
config:
  failure.emails: user@qq.com

nodes:
  - name: testA
    type: command
    config:
      command: echo 'hello wrold'
  - name: testB
    type: command
    config:
      command: echo 'hello wrold'
```
其中nodes节点下的就是执行的任务，commnad写的就是该任务需要执行的指令，azkaban还支持其他job类型，具体见[官方文档](https://azkaban.readthedocs.io/en/latest/jobTypes.html)
我暂时只用到commond
4.同时新建一个flow20.project文件，里面只需要一条内容，如下
azkaban-flow-version: 2.0
5.将flow文件和flow20.project文件打包成 **【项目名.zip】**，然后在azkaban页面点击upload，上传该zip文件
6.点击项目名就可以看到刚创建的job，然后点击execute flow-schedule，就可以配置cron表达式，设置该任务的运行周期
![](5.png)
![](6.png)
7.以后对于任务的新增和命令修改，都只能通过在azkaban页面点击download下载zip文件，然后修改里面的内容，再重新上传来完成
      