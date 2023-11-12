---
title: Nginx学习笔记
date: 2023-11-04 11:25:09
tags:
---

### docker启动nginx

- 拉取nginx最新版本： `docker pull nginx` 
  - 启动nginx：`docker run --name nginx-test --mount type=bind,source=/docker/nginx/html,target=/usr/share/nginx/html --mount type=bind,source=/docker/nginx/nginx,target=/etc/nginx --mount type=bind,source=/docker/nginx/logs,target=/var/log/nginx -p 8081:80 -d nginx`
    - 其中`/usr/share/nginx/html`是默认的nginx容器页面路径
    - `-p 8081:80`是端口映射配置
    - `/etc/nginx`是默认的nginx容器配置目录
    - `d` 表示后台运行
    - 最后的`nginx`表示使用nginx镜像启动
  - 复制nginx容器配置文件目录到本地目录：`docker container cp nginx-test:/etc/nginx/nginx.conf .`
  - 重新启动nginx
  - docker容器内执行nginx指令
    - reload：`docker exec -it nginx-test nginx -s reload`
    - test：`docker exec -it nginx-test nginx -t`

<!-- more -->
### 负载均衡的策略

- 轮询
  - 配置
  ```conf
  upstream big_server_com {
    server 127.0.0.1:8081 weight=5; #weight 权重
    server 127.0.0.1:8001 weight=5;
  }
  ```
- 权重
  - 配置：如上
  - weight的值没有指定的总量，是相对值
- ip_hash
  - 用于多服务器之间session共享
  - iphash，同一个ip固定访问同一个服务器
  - 如果该服务器挂掉，会访问轮询列表中的下一个服务器，并且不会丢失session
  - 访问用户不多的情况下，会导致负载不均衡
  - 配置
  ```conf
  upstream big_server_com {
    ip_hash;
  }
  ```
- url_hash
  - 页面hash，同一个页面固定访问同一个服务器
  - [第三方插件](https://github.com/replay/ngx_http_consistent_hash)
  - 插件安装
    - [下载](https://github.com/replay/ngx_http_consistent_hash)
    - 在nginx的configure目录下执行`./configure --prefix=/usr/local/nginx --add-module=/pluginLocation/ngx_http_consistent_hash`
  - [配置方式](https://www.nginx.com/resources/wiki/modules/consistent_hash/)
  ```conf
  upstream somestream {
    consistent_hash $request_uri;
    server 10.50.1.3:11211;
    server 10.50.1.4:11211;
    server 10.50.1.5:11211;
  }
  ```
- 图片资源的共享
  - FastDFS
  - NFS
  - HDFS
  - OSS

### 如何保持session一致性

- cookie保存
- tomcat分发
- session值放进数据库
- token
- [redis共享session](https://www.cnblogs.com/CF1314/p/14699780.html)
- ip_hash

### 修改设备状态

- down
  - 增加这一个配置后，该服务器将会被忽略
  - 配置：
  ```conf
  upstream big_server_com {
    server 127.0.0.1:8081 weight=5 down; #down 忽略该服务器
    server 127.0.0.1:8001 weight=5;
  }
  ```
- backup
  - 加上这一个配置后，该服务器成为备机
  - 配置
  ```conf
  upstream big_server_com {
    server 127.0.0.1:8081 weight=5 backup; #backup 备机
    server 127.0.0.1:8001 weight=5;
  }
  ```
- max_fails和fail_timeout
  - max_fails表示最大失败请求次数，默认是1；fail_timeout（默认10s）表示在指定时间内达到max_fails次数后就放弃尝试，判断该服务器节点为宕机状态
  - 在服务器宕机后的fail_timeout时间后，重新尝试
  - 配置
  ```conf
  upstream big_server_com {
    server 127.0.0.1:8081 weight=5 max_fails=2 fail_timeout=10s; 
    server 127.0.0.1:8001 weight=5;
  }
  ```

### gzip压缩

- 写在server块内
```conf
gzip on;                     #开启gzip压缩功能
gzip_min_length 10k;         #设置允许压缩的页面最小字节数; 这里表示如果文件小于10个字节，就不用压缩，因为没有意义，本来就很小.
gzip_buffers 4 16k;          #设置压缩缓冲区大小，此处设置为4个16K内存作为压缩结果流缓存
gzip_http_version 1.1;       #压缩版本
gzip_comp_level 2;           #设置压缩比率，最小为1，处理速度快，传输速度慢；9为最大压缩比，处理速度慢，传输速度快; 这里表示压缩级别，可以是0到9中的任一个，级别越高，压缩就越小，节省了带宽资源，但同时也消耗CPU资源，所以一般折中为6
gzip_types text/css text/xml application/javascript;      #制定压缩的类型,线上配置时尽可能配置多的压缩类型!
gzip_disable "MSIE [1-6]\.";       #配置禁用gzip条件，支持正则。此处表示ie6及以下不启用gzip（因为ie低版本不支持）
gzip_vary on;    #选择支持vary header；改选项可以让前端的缓存服务器缓存经过gzip压缩的页面; 这个可以不写，表示在传送数据时，给客户端说明我使用了gzip压缩
```

- 如果成功，会在浏览器的请求头里出现两个字段
```conf
Content-Encoding: gzip
Transfer-Encoding: chunked
```

### worker_processes配置

- 默认为1
- 结合cpu数量设置，一般要设置为cpu的核心数量或核心数量x2
  - linux 通过 `lscpu`来查看cpu核心数
- `worker_rlimit_nofile`：Nginx worker进程最大打开文件数，不可超过linux最大文件打开数，最大一般为65535
  - linux最大打开数设置`ulimit -n 65535`
- 连接数`worker_connections`
  - 配置
  ```conf
  events {
    worker_connections 1000;
  }
  ```
  - 不可超过linux的最大socket连接数
    - 设置地址`/proc/sys/net/core/somaxconn`