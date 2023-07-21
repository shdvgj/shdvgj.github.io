---
title: 如何低成本开发并发布小程序（微信云托管+Cloudflare worker）
date: 2023-07-18 14:44:30
categories: 
- 学习
---

先说结论，成本仅集中于微信云托管的费用，cloudflare全用的免费套餐。

## 前提

下面的前三步可参考我的[建站指南](https://blog.cindyforever.com/2023/04/12/2023/04/how_to_create_new_site/)。
- 一个Cloudflare的账户
- 一个已经申请成功的https域名
- https证书已绑定到cloudflare
- 一个微信小程序账号
- 小程序已开通云托管功能

## 微信云托管开发流程

### 什么是微信云托管？
简单说就是微信的微型服务器，非常方便部署一些只需要简单后台的应用。相比于单独买服务器，我这些天体验微信云托管下来得出的优缺点如下：

<!-- more -->

#### 优点
- 按量计费：独立开发或者小网站最大的问题可能是服务器性能过剩，完全没有用户访问的情况下还得交那么多的服务器费用。而云托管可以按量计费，没有用户访问的情况下几乎没什么费用。
- 微信天然鉴权：这个实际用起来非常的爽，小程序调用云托管会在请求头里面自带openid和unionid，大大节省了来回调微信接口来进行用户认证的麻烦。
- 节省https证书费用：如果单独买服务器，你还需要注册**国内的https证书**，小程序才能够调用，如果你不幸买了国外的服务器和证书，那是没办法和小程序接通的。**而用云托管的话不用买证书小程序就能调用。**
- 安全：自带防ddos攻击，防爬取，这也能比买服务器省心不少。
- 自带数据库：可以存入其自带的mysql数据库，小型项目用起来确实非常方便。大型项目就算了，如果你真的有很多的数据要存入数据库，**这个云托管的数据库费用可能会让你怀疑人生。**

#### 缺点
- 没有缓存功能：不仅仅是没有redis服务，**而且本地缓存也不能用**。这点配合着上面所说的按量计费，就会非常蛋疼，这意味着如果你不额外购买缓存服务，那每次服务调用都要耗费服务器和数据库资源，费用会非常惊人。
- 代码质量要求高：由于是按量计费，所以你代码最好没什么恶性bug，否则可能会大量耗费服务器资源，导致费用激增。
- 访问受限：这是国内服务器的普遍问题，也就是GFW导致的服务器访问外网受限。这点通过后续介绍的cloudflare来进行解决。

### 开发流程
- 进入公众号后台-云服务-云托管，点击开通。
![](wxcloud1.png)
- 进入云托管后台，点击模版部署，这里有各种语言的模版，选择你习惯用的语言。
![](wxcloud2.png)
- 接下来让你输入数据库密码，然后点击部署就完成了。
![](wxcloud3.png)

这上面几步就生成了一个服务，可以在服务列表里查看服务名称。

在服务页面，可以看到部署发布的几个选项，你可以将本地代码上传到github，然后将github和服务绑定，这样你每次提交到github的时候就会自动部署。
![](wxcloud4.png)

我个人认为相比上面的部署方式，有个更好用的工具，就是微信官方的[云托管CLI](https://cloud.weixin.qq.com/cli/guide.html)。使用方式：
- 安装：`npm i -g @wxcloud/cli` 。
- 登录：`wxcloud login --appId <微信 AppId> --privateKey <秘钥>`，密钥在[微信云托管控制台-设置-全局设置-CLI密钥](https://cloud.weixin.qq.com/cloudrun/settings/other)生成。
- 项目初始化：`wxcloud migrate`，这条命令会根据你的项目自动生成容器文件。
- 部署：`wxcloud deploy`，做完上述几步后，每次发布只需要敲这个命令就可以自动完成部署。
```sh
$ wxcloud deploy

Wxcloud CLI 2.0.1-beta.15
? 请选择环境 test (test-environment)
? 请选择服务 test-service

<构建日志>

✔ 云托管产物上传
✔ 云托管版本创建
public       | ████████████████████████████████████████ | 2/2 Files
.next/static | ████████████████████████████████████████ | 12/12 Files
✔ 静态资源上传文件成功

部署开始于 2022-05-19 15:40:33

<部署日志>

✔ 部署完成
  服务 cloudkit 访问地址:
  > demo.ap-shanghai.run.tcloudbase.com
```

### 开发要点

- 环境变量：一些敏感信息或者会进行线上修改的变量在[服务列表-服务设置-环境变量]里面指定。
- 实例数量：默认的实例最小数量是0，这导致云托管在没有用户访问的情况下，隔一段时间就会完全停掉所有服务，此时访问小程序时，是无法调用任何后端服务的。为了更良好的用户体验，建议最小实例数量设置为1。设置地址[服务列表-服务设置-实例副本数]
- 节省资源：这点非常重要，按量计费的情况下，能省则省。
  - 爬虫类服务，尽量不要一次性爬取所有数据，可以惰性爬取，等待用户点击到那个页面再进行爬取。
  - 前端和后端最好都实现缓存，前文说过，云托管没有缓存功能，因此后端的缓存需要别的方式实现，**后面会讲一个可行的方案。**
  - 尽量使用前端进行数据处理，因为后端处理需要费用，前端不需要。
  - 如果一定要用后端处理数据，也尽量用代码处理，不要用sql进行复杂的查询和数据处理，因为在云托管的费用列表里，数据库算力是最贵的一项，高出别的所有资源一档。
![](wxcloud5.png)

### 小程序调用云托管

- 在小程序的app.js中增加一个通用的call方法
```js
/**
   * 封装的微信云托管调用方法
   * @param {*} obj 业务请求信息，可按照需要扩展
   * @param {*} number 请求等待，默认不用传，用于初始化等待
   */
  async call(obj, number=0){
    const that = this
    if(that.cloud == null){
      that.cloud = new wx.cloud.Cloud({
        resourceAppid: 'WXAAA', // 微信云托管环境所属账号，服务商appid、公众号或小程序appid
        resourceEnv: 'prod-001', // 微信云托管的环境ID
      })
      await that.cloud.init() // init过程是异步的，需要等待init完成才可以发起调用
    }
    try{
      const result = await that.cloud.callContainer({
        path: obj.path, // 填入业务自定义路径和参数，根目录，就是 / 
        method: obj.method||'GET', // 按照自己的业务开发，选择对应的方法
        // dataType:'text', // 如果返回的不是json格式，需要添加此项
        header: {
          'X-WX-SERVICE': 'xxx', // xxx中填入服务名称（微信云托管 - 服务管理 - 服务列表 - 服务名称）
          // 其他header参数
        }
        // 其余参数同 wx.request
      })
      console.log(`微信云托管调用结果${result.errMsg} | callid:${result.callID}`)
      return result.data // 业务数据在data中
    } catch(e){
      const error = e.toString()
       // 如果错误信息为未初始化，则等待300ms再次尝试，因为init过程是异步的
      if(error.indexOf("Cloud API isn't enabled")!=-1 && number<3){
        return new Promise((resolve)=>{
          setTimeout(function(){
            resolve(that.call(obj,number+1))
          },300)
        })
      } else {
        throw new Error(`微信云托管调用失败${error}`)
      }
    }
  }
```
- 方法中调用
```js
const app = getApp()
Page({
  async onLoad(){
    const res = await app.call({
      path:'/'
    })
    console.log('业务返回结果',res)
  }
})
```
- 别的详细说明参考[官方文档](https://developers.weixin.qq.com/miniprogram/dev/wxcloudrun/src/development/call/mini.html)。


## cloudflare开发流程

### 为什么用cloudflare？

从前面介绍来说，讲到云托管的几个缺点，其中对于业务开发来说最有影响的，一个是没有缓存功能，另一个是访问外网受限。第一个可能导致云托管费用激增，第二个则限制了你的小程序功能，比如我想做一个外网的爬虫项目，单纯用云托管就没办法做到。

而这两点都可以通过cloudflare的功能来解决。

### worker解决外网受限

worker是cloudflare的一个功能，简单说可以看成是一个路由，也可以认为是一个微型服务，总之，通过将你的域名绑定到worker，然后再通过小程序访问这个域名，就可以对小程序的请求进行转发处理，这么一个流程：【小程序 -》 你的域名worker -》 外网】。**这当中唯一需要保证的就是小程序可以访问到你的域名**，一般情况下，你的域名只要没做什么黄赌毒之类的事儿，应该是没问题的。

worker部署：详情看我的另一篇博文[《如何通过cloudflare的worker处理复杂后台逻辑》](https://blog.cindyforever.com/2023/07/07/2023/07/how-to-build-project-by-cloudflare/)

### kv实现缓存功能

kv是cloudflare另一个功能，就是其自带的键值对存储功能，用来当缓存简直再合适不过。以下配置和内容需要先看过上面的worker博文才好理解。

- 在cloudflare的首页，点击[Workers & Pages]
- 点击[KV]
- 选择[create a namespace]
- 输入name后，点击ADD完成新增
- 复制你的[KV]的[name]和[ID]
- 在你的本地worker项目中，在`wrangler.toml`中增加配置：
```js
kv_namespaces = [
    { binding = "YOUR_KV_NAME", id = "YOUR_KV_ID" }
]
```
- 使用`wrangler deploy`命令完成部署，`KV`会自动绑定到你的`worker`上
- `worker`项目中`KV`的使用（尽管官方文档写了直接在项目中使用KV的`NAME.get`和`NAME.put`就行，但是我怎么试都报错，所以还是直接用[API文档](https://developers.cloudflare.com/api/operations/workers-kv-namespace-list-namespaces)的方式调用）：
  - 写入值：
```js
    let detailResponse = await fetch(`https://api.cloudflare.com/client/v4/accounts/${ACOUNT_ID}/storage/kv/namespaces/${NAME_SPACE}/bulk`, 
	{ method: "PUT", body: JSON.stringify([
			{
			  "base64": false,
			  "expiration_ttl": 86400,
			  "key": reqData.key,
			  "value": reqData.value
			}
		  ]),
		  headers: {
			'Content-Type': 'application/json',
			'X-Auth-Key': X_AUTH_KEY,
			'X-Auth-Email': X_Auth_Email
		  }
	});
```
  - 读取值：
```js
    let detailResponse = await fetch(`https://api.cloudflare.com/client/v4/accounts/${ACOUNT_ID}/storage/kv/namespaces/${NAME_SPACE}/values/${key}`, 
	{ method: "GET",headers: {
		'Content-Type': 'application/json',
		'X-Auth-Key': X_AUTH_KEY,
		'X-Auth-Email': X_Auth_Email
	  }});
```

接下来通过前面所说的worker转发路由功能，就可以远程调用kv来进行缓存。

## 总结

如上所述，云托管主要解决了：
- 国内域名备案问题，证书问题
- 服务器费用过高问题
- 小程序鉴权问题

而cloudflare主要解决了：
- 云托管外网受限问题
- 云托管没有缓存功能问题