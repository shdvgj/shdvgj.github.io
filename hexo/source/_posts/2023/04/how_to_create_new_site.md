---
title: Namcheap+CloudFlare简单建站
date: 2023-04-12 22:01:51
tags:
- 建站
categories: 
- 学习
---

### 购买服务器

- 需要基于流量和内容进行选择，如果打算服务于国内，选择国内的服务器提供商，比如阿里云或者腾讯云等，如果需要某些国内服务器做不到的功能，比如调用openai的接口，则最好是买个国外的服务器。
- 需要注意的是，如果打算基于微信小程序或者公众号开发，必须要满足域名通过国家备案，而备案的必要条件是所在的服务器是国内供应商。因此如果是为了开发小程序而买了个国外服务器，最终很可能因为无法备案而无法满足你的需求。
- 无论国内外，最便宜的套餐普遍都是1核1G，20GB硬盘空间，1Mbps带宽。国外有Vultr，Linode，DigitalOcean，AWS Lightsail等，价格普遍在5美刀/月左右。国内可选择阿里云，百度云，腾讯云，华为云等，价格20-100元人民币/月不等。
- 如果是开发小程序和公众号，有另一种选择，跳过购买域名和服务器，直接使用腾讯的[云托管平台](https://cloud.weixin.qq.com/)


### 购买域名和ssl证书

需要注意，目前情况而言，购买域名的同时也几乎必须得购买ssl证书，因为现在http的网站已经不再被各大浏览器所推荐，流量优先级会被调低，甚至小程序直接不支持指向http网站，如果建站的目的是希望获得收入，那拥有ssl证书的https网站是必须的。

- 国内
  - 易名中国：域名价格约为50元人民币/年起，SSL证书价格约为100元人民币/年起。
  - 西部数码：域名价格约为60元人民币/年起，SSL证书价格约为100元人民币/年起。
  - 新网：域名价格约为50元人民币/年起，SSL证书价格约为200元人民币/年起。
  - 阿里云：域名价格约为60元人民币/年起，SSL证书价格约为300元人民币/年起。
  - 巨牛网：域名价格约为50元人民币/年起，SSL证书价格约为150元人民币/年起。

- 国外
  - GoDaddy：域名价格约为$12/年起，SSL证书价格约为$70/年起。
  - Namecheap：域名价格约为$8/年起，SSL证书价格约为$9/年起。
  - Google Domains：域名价格约为$12/年起，SSL证书价格约为$20/年起。
  - Network Solutions：域名价格约为$35/年起，SSL证书价格约为$60/年起。
  - Name.com：域名价格约为$10/年起，SSL证书价格约为$9/年起。

<!--more-->

### 生成CSR

- 什么是CSR
> CSR code (Certificate Signing Request) is a specific code and an essential part for the SSL activation. It contains information about website name and the company contact details. For many reasons, the code should be created on the hosting server end. On some servers, it is the obligatory condition.

  总之CSR是一段关于你网站的身份信息的加密文件，对于激活ssl证书是必须的，在服务器端生成。

- 通过openssl命令生成
  有很多种方式生成csr文件，甚至有在线网站可以直接生成，通常不建议在线生成，因为csr是ssl的必要依赖，关联到网站的安全性，最好是不要冒泄露的风险。
  - 新建.key文件夹，执行如下命令
  ```shell
  npm install openssl
  cd .key
  openssl req -nodes -newkey rsa:2048 -keyout $serverName.key -out $serverName.csr
  ```
  注意，$serverName用你的网站名替换，会生成两个文件，以你网站名为命名的key文件和csr文件。
  生成时会要求你填入国家，公司，email等，根据提示填入。
  ***国内域名商的流程会不太一样，因为是要备案实名制，所以还会要求身份证信息等，而且是界面操作，无需通过以上命令行***

- 复制csr文件中的内容，通常如下：
![](csr-content.png)
  复制时要包含全部内容，不要略去BEGIN和END那一行。

- 将csr粘贴到域名供应商配置界面，以namecheap为例，在你已购买的ssl证书界面点击active。
![](activate_ssl.png)
  将刚才csr文件中的内容粘贴到该界面中。
![](copy_csr.png)
  下一步中选择Add CNAME record
![](act_dcv_choice.png)
  下一步中输入你的电子邮箱

- 完成后进入证书管理界面，按照如下操作
![](act_done_1.png)
  此时会获取到下一步会用到的cname的host和target
![](cname_content.png)

### 注册DNS  
  一般的域名供应商都会提供有dns服务，namecheap也有，但是功能都比较基础，通常建议使用第三方DNS供应商。国外的有google，cloud flare，国内也有阿里，百度等。
  以cloudflare为例
- 点击add site，输入你的域名，选择free计划
- 输入你的域名和服务器的公网ip
- 在namecheap中的namerserver界面选择Custom DNS，输入cloudflare提供的两个dns服务器地址
![](namecheap_dns.png)
- 进入cloudflare的dns管理界面，添加一条cname记录，输入之前步骤中得到的cname的host和target，注意Proxy status这一栏要改成DNS only
![](cloudflare_dns.png)
- ***到这一步之后，ssl证书发放商就会把ssl证书发到你的邮箱***，按照官网的说法，这个时间可能24到48小时，一般用不了那么久，一小时内就可以收到，如果没收到，去namecheap的[ssl校验网站](https://decoder.link/)看下，如果有出错，那重新捋一遍上面的步骤。

### 安装SSL证书

- 收到ssl证书后，通常发放邮件长下面这样，将邮件中的附件解压，包含三个文件，ca-bundle文件，crt文件和p7b文件
![](ssl_email.png)
- 将ca-bundle文件和crt文件上传到服务器，并执行如下命令
```shell
$ cat your_domain.crt > your_domain_chain.crt ; echo >> your_domain_chain.crt ; cat your_domain.ca-bundle >> your_domain_chain.crt
```
- 服务器安装ssl证书的方式有很多种，最常用的方式是nginx，以nginx为例，安装完nginx之后，进入/etc/nginx/conf.d/目录，新增一个your_domain.conf配置文件，配置文件内容如下
```js
server {
    listen       80;
    server_name  www.example.com;
    return 301 https://$server_name$request_uri;
}
server {
    listen       443 ssl;
    # 域名，多个以空格分开
    server_name  www.example.com;
    
    # ssl证书地址
    ssl_certificate     /home/.ssl/example.crt;  # crt文件的路径
    ssl_certificate_key  /home/.key/example.key; # key文件的路径，即生成csr文件步骤中的key文件
    
    # ssl验证相关配置
    ssl_session_timeout  5m;    #缓存有效期
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;    #加密算法
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;    #安全链接可选的加密协议
    ssl_prefer_server_ciphers on;   #使用服务器端的首选算法

    location / {
      proxy_pass http://localhost:8080/; # 服务器地址
    }
}
```
- 启动nginx，即可在公网中访问到你的服务器了