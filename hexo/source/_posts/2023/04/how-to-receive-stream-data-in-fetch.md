---
title: 使用fetchAPI获取post请求中的stream数据
date: 2023-04-24 16:03:35
tags:
- node.js
categories: 
- 学习
---

## 问题起源

- 希望用miniprogram实现调用chatGPT接口
- miniprogram国内直接调用api.openai.com接口不通，需要国外购买服务器建站中转，即miniprogram-国外服务器-openai接口来实现
- miniprogram线上环境无法调用任何没在国内备案的网站，因此无法通过上述中转方案实现
- 有两套替代方案，购买国内备案服务器，或者直接使用miniprogram云托管来实现，选择了后者，因为无需备案
- miniprogram云托管中转调用国外服务器，再调用openai接口，可以实现
- miniprogram云托管单个接口最大返回时间固定为15秒，而openai接口的完整数据返回往往大于这个时间
- 通过设定接口参数 `stream:true` 来实现流式传输，即接口数据可以先部分返回，通过长连接持续传输
- 流式传输有两种方案，`Server-Sent Events` 和 `fetch stream`

## SSE（Server-sent Event）方案实现

参考[mdn文档](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events)和[阮一峰sse教程](https://www.ruanyifeng.com/blog/2017/05/server-sent_events.html)

### 创建一个EventSource实例

```js
const evtSource = new EventSource("ssedemo.php");
const evtSource = new EventSource("//api.example.com/ssedemo.php", {
  withCredentials: true,
});
```

### 监听返回数据

```js
evtSource.onmessage = (event) => {
  const newElement = document.createElement("li");
  const eventList = document.getElementById("list");

  newElement.textContent = `message: ${event.data}`;
  eventList.appendChild(newElement);
};
```

### 定制监听事件

在下述代码中，将会监听返回数据中事件字段中的`ping`

```js
evtSource.addEventListener("ping", (event) => {
  const newElement = document.createElement("li");
  const eventList = document.getElementById("list");
  const time = JSON.parse(event.data).time;
  newElement.textContent = `ping at ${time}`;
  eventList.appendChild(newElement);
});
```

### 方法尝试结论

最终并不能实现需求，原因在于sse天生只支持get方式获取数据，对于post请求无法支持。
***另外发现miniprogram不支持server-sent events。***

## Fetch获取数据流

参考[阮一峰fetch教程](https://www.ruanyifeng.com/blog/2020/12/fetch-tutorial.html)
核心处理方式就是利用fetch异步获取流的方式。

```js
let reqData = JSON.stringify({
    "user": "superman"
});
const response = await fetch('https://example.com/api', {
    method: 'POST',
    body: reqData
});
const reader = response.body.getReader();
const decoder = new TextDecoder();
while (true) {
    const {value, done} = await reader.read();
    if (done) {
        break;
    };
    let str = decoder.decode(value);
    console.log(str);
}
```

这样获取到的数据格式如下：

```text
data: some text
data: another message
data: with two lines
```

目前这样子的形式已经算是获取到数据了，但是不是常见的json格式，因此需要进行进一步的分析处理。
