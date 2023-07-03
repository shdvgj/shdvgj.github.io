---
title: 每日文章收集-20230629
date: 2023-06-29 13:06:53
comments: true
categories: 
- 每日文章收集
---

# 【博文一览】

### [background-removal-js：免费开源的背景移除脚本](https://github.com/imgly/background-removal-js)

通过该脚本可以在浏览器环境中直接移除图片的背景，无需担心多余的性能开销或者隐私顾虑。

其主要的功能特点有：
- 浏览器上的背景移除：消除了额外的服务器成本。通过利用本地设备的计算能力，用户可以享受快速高效的背景去除过程。
- 数据保护：完全运行于浏览器端。无需任何数据传输到别的服务器，因此数据是绝对安全的。
- 与[IMG.LY的CE.SDK](https://img.ly/creative-sdk?utm_source=github&utm_medium=project&utm_campaign=bg-removal)（作者的另一个项目） 实现了无缝集成，使开发人员能够轻松地将强大的浏览器内图像抠图和背景去除功能整合到他们的项目中。
![](backgroundremove1.png)

本地使用安装步骤如下
<!-- more -->
- NPM安装
``` sh
npm install @imgly/background-removal
```
- 代码引用：注意首次调用需要下载额外模块，因此会花费比较多时间。
``` js
import imglyRemoveBackground from "@imgly/background-removal"

let image_src: ImageData | ArrayBuffer | Uint8Array | Blob | URL | string = ...;

imglyRemoveBackground(image_src).then((blob: Blob) => {
  // The result is a blob encoded as PNG. It can be converted to an URL to be used as HTMLImage.src
  const url = URL.createObjectURL(blob);
})
```
- 进阶设置
``` js
type Config = {
  publicPath: string; // The public path used for model and wasm files
  debug: bool; // enable or disable useful console.log outputs
  proxyToWorker: bool; // Wether or wether not to proxy the calculations to a webworker. (Default true)
  model: 'small' | 'medium'; // The model to use. (Default "medium")
};
```

### [fly.io：靠近用户运行的全栈应用程序和数据库](https://fly.io/)

fly.io是一个目前流行的云平台，专为运行全栈应用程序和数据库而设计。它的主要目标是使应用程序更靠近用户，以提供更快的响应时间和更好的用户体验，同时简化了开发人员的工作流程。

- 安装flyctl
    - macos：运行`brew install flyctl`或者`curl -L https://fly.io/install.sh | sh`
    - linux：运行`curl -L https://fly.io/install.sh | sh`
    - windows：powershell运行`pwsh -Command "iwr https://fly.io/install.ps1 -useb | iex"`
- 注册，输入以下命令跳转到注册页面，或者直接到注册页面进行注册。
```sh
fly auth signup
```
- 使用 `fly auth login` 登陆fly.io的账户
- 部署hellofly项目
```sh
## 直接拉取docker项目并完成部署
fly launch --image flyio/hellofly:latest
```
- 部署过程中要求选择地区，就近选择
```sh
Choose a region for deployment: Chicago, Illinois (US) (ord)
```
- 询问是否需要Postgres或者Redis，按需选择
- 最后询问是否需要部署，部署完成后会在本地生成一个fly.toml的配置文件
```sh 
app = "hellofly"
primary_region = "ord"

[build]
  image = "flyio/hellofly:latest"

[http_service]
  internal_port = 8080
  ...
```
- 使用`fly deploy`部署完成后，可通过`fly status`命令检查项目状态
- 使用`fly open /<your-name>`命令可以在fly.io的网站访问到刚刚部署的项目
![](flyio1.webp)

### [TypeID：一个受到Stripe ID启发的类型安全、可排序的全局唯一标识符（ID）](https://github.com/jetpack-io/typeid)

TypeIDs是UUIDv7的一种现代、类型安全的扩展。它在设计上受到Stripe API中前缀类似用法的启发。包含三个部分：

- 类型前缀（由小写字母组成的最多63位的字符串）
- 一个下划线
- 一个128位的UUIDv7，以26个字符的base32字符串编码（根据Crockford的小写字母表）
```
  user_2x4y6z8a0b1c2d3e4f5g6h7j8k
  └──┘ └────────────────────────┘
  type    uuid suffix (base32)
```

TypeID有以下特性：
- 类型安全：您不会意外地在期望使用帖子ID的地方使用用户ID。在调试过程中，通过类型前缀，您可以立即了解TypeID引用的实体类型。
- 与UUID兼容：TypeID是UUID的超集。它们基于即将发布的UUIDv7标准。如果您解码TypeID并移除类型信息，您将得到一个有效的UUIDv7。
- 可K排序：TypeID是可K排序的，可以用作数据库中的主键，同时确保良好的局部性。与完全随机的全局ID（例如UUIDv4）相比，后者通常在数据库局部性方面表现不佳。
- 精心编码：基于base32的编码是URL安全的、不区分大小写的、避免了歧义字符，可以通过双击选择复制粘贴，并且与传统的十六进制编码（UUID使用的编码方式）相比，它是一种更紧凑的编码（26个字符对比36个字符）

命令列表
- 安装命令
```sh
curl -fsSL https://get.jetpack.io/typeid | bash
```
- 创建新的TypeID
```sh
$ typeid new prefix
prefix_01h2xcejqtf2nbrexx3vqjhp41
```
- 将TypeID解码位UUID
```sh
$ typeid decode prefix_01h2xcejqtf2nbrexx3vqjhp41
type: prefix
uuid: 0188bac7-4afa-78aa-bc3b-bd1eef28d881
```
- 将UUID编码为TypeID
```sh
$ typeid encode prefix 0188bac7-4afa-78aa-bc3b-bd1eef28d881
prefix_01h2xcejqtf2nbrexx3vqjhp41
```

### [梵高艺术展](https://www.vangoghmuseum.nl/en)

![](vangogh1.png)
这个网站介绍了关于梵高的生平和艺术作品，并还有出售梵高艺术展的门票。

正在举办的艺术展
![](vangogh2.png)
门票也不算贵，成人20欧元，未成年人免费，相比起国内，算是相当厚道。
![](vangogh3.png)

网站上展示梵高的1515副画作，并提供下载。
![](vangogh4.png)
![](vangogh5.png)

以及梵高的故事。
![](vangogh6.png)

### [未来的CSS：状态容器查询](https://ishadeed.com/article/css-state-queries/)

状态容器查询。它是一种可以根据元素的状态和属性来应用样式的能力。通过状态容器查询，开发者可以根据元素在不同状态下的宽度、高度、可见性等属性，动态地调整其样式。这项功能将提供更强大的响应式设计和样式控制的能力，使网页设计更加灵活和适应不同设备和交互情境。

例如这样子进行设置：
```css
.card-wrapper {
  container-type: inline-size;
  container-name: card;
}

@container card (min-width: 400px) {
  .card {
    display: flex;
    align-items: center;
  }
}
```
通过上述语法，当容器宽度到达400px时，就会触发`.card {display: flex;align-items: center;}`的样式。

下面的图片可以更好的说明该语法的功能。
![](css1.png)

更多的语法说明详见作者的另一篇文章(https://ishadeed.com/article/css-container-style-queries/)。

