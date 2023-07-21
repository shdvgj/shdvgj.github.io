---
title: 如何通过cloudflare的worker处理复杂后台逻辑
date: 2023-07-07 14:08:10
categories: 
- 学习
---

## 前提准备

- 建站：一系列准备工作可看另一篇我的[建站指南](https://blog.cindyforever.com/2023/04/12/2023/04/how_to_create_new_site/)。
- 需要本地安装node

## 新建worker

- 在cloudflare的首页，点击[Workers & Pages]
- 选择[create application]
- 点击[create worker]
- 输入Name，点击Deploy

<!-- more -->

## 部署代码到worker

### 获取API KEY

- 登录cloudflare后台，点击用户头像 -〉点击My Profile
- 选择API Tokens -〉Create Token
- 选择Edit Cloudflare Workers这一栏，点击Use template
![](APIKey.png)
- 正常情况下API权限已经按照模版配置好了，不用修改，如需微调，参考[API文档](https://developers.cloudflare.com/fundamentals/api/)
- 编辑Account Resources和Zone Resources指向你的账号和域名
- 点击Continue to summary，下一步点击Create Token
- 此时出现API token和用法示例，**注意，立刻保存这个token，后续你将无法再找到**
![](APIToken.png)



### 安装wrangler

`wrangler` 是cloudflare官方的一个部署包，可以方便的将你的较复杂的后台node代码部署到指定的router上。

- 安装命令
```sh
npm install -g wrangler
```
- 检查版本
```sh
wrangler --version
```

### 初始化代码

`wrangler` 有一系列的模版代码，通过命令`npx wrangler generate [your-app-name] [modle-name]`可以轻松构建一个可部署的`wrangler`项目

- 构建router
```sh
npx wrangler generate my-app worker-router
```
- 构建测速项目
```sh
npx wrangler generate my-app worker-speedtest
```
- sentry错误跟踪
```sh
npx wrangler generate my-app mhart/cf-sentry
```
还有很多，不一一列举了，可以去[cloudflare的模版代码库](https://github.com/cloudflare/workers-sdk/tree/main/templates)找。
![](template1.png)

在初始化的项目里就可以构建你的后台逻辑了。

### 编辑配置文件

- 在生成的`wrangler.toml`文件中，进行如下配置
```sh
## 部署的worker名称
name = "example"
## 项目主页
main = "index.js"
## 兼容性日期，可以不动
## 根据[说明](https://developers.cloudflare.com/workers/platform/compatibility-dates/)，最好实时更新到最新日期，以支持最新的cloudflare功能
compatibility_date = "2023-07-10"

# 从cloudflare后台，进入你的网站，右下角可以看到Account ID
account_id="your_acount_id"
# 是否部署到cloudflare的dev域名
workers_dev = false
```
- 在项目中创建`.env`文件
- 创建环境变量`CLOUDFLARE_API_TOKEN=<YOUR_API_TOKEN_VALUE>`，将之前获取到的API_TOKEN写入

### 部署

- 配置好上述环境变量后，就可以直接运行`wrangler deploy`进行部署
- 也可以运行`CLOUDFLARE_API_TOKEN=XXX wrangler deploy`手动指定环境变量
