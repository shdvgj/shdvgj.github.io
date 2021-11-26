---
title: Express框架中使用get和post
date: 2021-11-26 10:06:45
tags: 
- node.js
- translate
categories: 
- 学习
- 搭建相关
---
之所以有这个文章，是因为遇到在nodejs中使用express框架直接接受post请求时，无法获取到body中的内容，于是google了一篇英文文章解决了这个问题。
翻译自[codeforgeek](https://codeforgeek.com/handle-get-post-request-express-4/)

---

GET和POST是两种用于构建REST接口的普通HTTP请求。两者的命名包含着不同的使用目的。
根据定义，GET请求从指定的地址抓取数据，POST请求则是发送数据到指定的地址。

Express框架提供了router()方法用于构建HTTP终端。下面让我们看下怎么使用Express来实现GET和POST请求。

## GET请求

在Express中使用GET请求非常直接。你首先必须创建一个express和router的实例。下面是实现的一小段代码。
```js
const express = require("express");
const router = express.Router();
const app = express();

router.get(‘/handle’,(request,response) => {
//code to perform particular action.
//To access GET variable use req.query() and req.params() methods.
});

// add router in the Express app.
app.use("/", router);
```
GET请求会缓存在浏览器历史中。这就是为什么不推荐使用GET请求发送敏感数据（密码，ATM取款码，等等）。你应该只使用GET请求从服务器获取数据。

## POST请求

Express需要一个额外的中间件模块提取POST请求中的数据。这个中间件叫做"body-parser"。我们需要在Express的实例中安装和配置它。
你可以使用如下命令安装body-parser。
```shell
sudo npm install --save body-parser
```
你需要在你的项目中导入这个包并告知Express去使用这个中间件。参考如下代码。
```js
const express = require("express");
const bodyParser = require("body-parser");
const router = express.Router();
const app = express();

//Here we are configuring express to use body-parser as middle-ware.
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());

router.post(‘/handle’,(request,response) => {
//code to perform particular action.
//To access POST variable use req.body()methods.
console.log(request.body);
});

// add router in the Express app.
app.use("/", router);
```
通过这种方式，你可以在Express框架中使用GET和POST请求。（译者注：注意先后顺序，body-parser的注入一定要在router的注入之前，否则也会不起作用）

---

后面的内容是博主写的demo，感觉也没必要发上来，就略过了。
