---
title: 每日文章收集-20230626
date: 2023-06-26 11:43:15
tags:
---

# 【今日关注】

## [vrite: 开源无界面内容管理系统（CMS），适用于编程博客、文档等](https://vrite.io/)

vrite开源的专用于写作技术内容的CMS系统。根据官网介绍，具有几个特点：
- 简洁的UI
- 内置看板界面，可以轻松管理任意规模的产品流程
- 所见即所得，支持markdown和快捷键，协同写作等
- 对技术写作量身定做，支持高亮，自动补全等
- 提供API和webhook，与各种前端界面轻松集成
- 完备的权限管理系统

### 基本概念介绍
Vrite使用三个概念来组织管理内容。
- 工作空间（Workspaces）：独立的单元，用于存储许多内容组（Content groups）、配置API访问或编辑体验的设置，以及关于整个团队及其角色的详细信息；可以创建或被加入到任意数量的工作区。
- 内容组（Content groups）：如果熟悉看板界面的话，这就相当于看板仪表中的列，用于组织内容碎片（Content pieces）。
- 内容碎片（Content pieces）：相当于看板界面中的卡片，用于编辑具体的内容，包含实际的内容和相关元数据。

### 使用工作空间
点击侧边栏切换工作空间。
![](vrite2.png)
在该界面选择或新增工作空间。
![](vrite3.png)

### 编辑内容
进入工作空间后，左边一栏是内容碎片的属性。右边是内容组和内容碎片。
![](vrite4.png)
点击内容碎片进入编辑界面后，将看到vrite所见即所得的特性。
- 弹起菜单：选择任意内容，会弹出浮动菜单用于选择样式，包括加粗，斜体，代码，链接等
![](vrite5.png)
- 段落菜单：在段落前或者空白区域键入“/”键就会弹出段落菜单。可以选择将段落定义为选定的格式。
![](vrite6.png)
- 链接菜单：将鼠标移动到一个包含超链接的内容上时，会出现该链接网页的预览内容。
![](vrite7.png)
- 支持任意markdown语法，同时还可使用快捷键快速定义格式。
- 特殊格式的内容会弹出一个编辑预览的内容块，例如图片，代码等。
![](vrite8.png)
- 顶部栏包含三个功能
  - 内容统计：统计段落，单词数，字符数，代码行数。
  - 导出：支持导出json，html，github适用的markdown格式。
  - 禅模式：仅保留内容，剔除所有的菜单和干扰，专注于内容编辑。
  ![](vrite9.png)
- 支持协同编辑，团队成员编辑过的内容会显示高亮并展示成员的名字。
![](vrite10.png)

### 插件

vrite的插件中心目前并不支持第三方的插件，只包含了四个和功能相关的插件。
- Hashnode：自动发布到[Hashnode](https://hashnode.com/)。
- Dev.to：自动发布到[Dev.to](https://dev.to/)。
- Medium：自动发布到[Medium](https://medium.com/)。
- GPT-3.5: 编辑内容时可以直接让chatGpt生成。

### JavaScript SDK

通过npm安装。
```js
npm i @vrite/sdk
```
安装完成后可以通过sdk提供的工具方法获取内容组和内容碎片等。
![](vrite11.png)

### 自托管

网站说明文档暂不建议现在进行自托管，因为vrite的稳定版本到2023年的第三季度才会发布。如果要进行自托管，网站也进行了一些说明。
项目结构基于[Turborepo](https://turbo.build/repo)
- 最重要的依赖是：
  - Typescript
  - Solid.js
  - Fastify
  - tRpc
  - Tiptap & ProseMirror 
  - Y.js & HocusPocus 
  - Monaco Editor 
  - Astro 
- 目录结构
  - /package
    - /backend
    - /components
    - /editor
    - /extensions
    - /scripts
    - /sdk/javascript
  - /apps
    - /backend/api
  - .......
- 环境变量
```sh
# Database
MONGO_URL=
REDIS_URL=
DATABASE=
# Security
SECRET=
# Domains
....
```
目前vrite的数据库基于MongoDB，Redis，S3。



# 【博文一览】

### [try命令：试一下你的命令并看下效果](https://github.com/binpash/try)

安装这个try命令
```sh
git clone https://github.com/binpash/try.git
```
使用try并执行别的命令，可以看到命令的预期效果。并会在结尾询问是否要真实执行该命令。
```sh
try pip3 install libdash

...
Defaulting to user installation because normal site-packages is not writeable
Collecting libdash
  Downloading libdash-0.3.1-cp310-cp310-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (254 kB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 254.6/254.6 KB 2.1 MB/s eta 0:00:00
Installing collected packages: libdash
Successfully installed libdash-0.3.1
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv

Changes detected in the following files:

/tmp/tmp.zHCkY9jtIT/upperdir/home/gliargovas/.local/lib/python3.10/site-packages/libdash/ast.py (modified/added)
/tmp/tmp.zHCkY9jtIT/upperdir/home/gliargovas/.local/lib/python3.10/site-packages/libdash/_dash.py (modified/added)
/tmp/tmp.zHCkY9jtIT/upperdir/home/gliargovas/.local/lib/python3.10/site-packages/libdash/__init__.py (modified/added)
/tmp/tmp.zHCkY9jtIT/upperdir/home/gliargovas/.local/lib/python3.10/site-packages/libdash/__pycache__/printer.cpython-310.pyc (modified/added)
/tmp/tmp.zHCkY9jtIT/upperdir/home/gliargovas/.local/lib/python3.10/site-packages/libdash/__pycache__/ast.cpython-310.pyc (modified/added)
<snip>

Commit these changes? [y/N] y
```

需要注意，这个命令并不是生成一个沙盒，它只是利用了linux系统的命名空间和overlayfs。因此不应该使用try去执行无法信任的命令。

### [Google在设置里隐藏了一个秘密的浏览器](https://matan-h.com/google-has-a-secret-browser-hidden-inside-the-settings/)

作者在安卓系统里发现一个惊人的bug，存在于安卓的各种app中，以及系统设置和谷歌适配的app里，这个浏览器甚至可以绕过家长控制！具体复现步骤如下：
（由于手头并没有安卓机，暂时无法复现，而且考虑到这篇文章是国外博主发布的，大概率也是原生安卓系统的bug，国内的安卓机应该无法复现。）
- 进入“设置”→“Google”（或任何允许选择账户的应用程序），点击“管理我的账户”。
- 然后进入“安全”选项卡。在其中，向下滚动直到找到“密码管理器”并点击。
- 点击右上角的“设置”图标。
- 向下滚动，直到出现“设置设备加密”。点击它，然后点击“了解有关设备加密的更多信息”。
- 现在你进入了浏览器。然后点击汉堡菜单（就是类似于“三”这样的按钮），然后点击“隐私政策”。
- 在顶部点击九个点，等待5秒钟（加载需要一些时间），然后点击“搜索”（如果你找不到搜索图标，你也可以向下滚动直到找到“Google”，然后点击它）。
- 从你的Google账户注销。
- 然后就得到了秘密浏览器，可以随意浏览任何网站。

这个bug作者已经反馈给谷歌，并得到了回复，估计不久就会被修复。
![](googlebug1.png)

### [WinGPT：windows3.1上的AI助理](https://www.dialup.net/wingpt/)

![](wingpt1.png)
作者在古早的windows3.1上开发了一个程序，实现了类似chatgpt的问答功能。在开发过程中克服了相当多的困难：
- 通过一系列努力在windows3.1上实现了tls1.3协议来调用chatgpt的接口，关于这部分的工作，看作者的另一篇[文章](https://www.dialup.net/wingpt/tls.html)。
- windows3.1上原生的UI界面非常难画，需要通过C语言代码一点点调试，而且也缺少一些如今通用的UI组件。
- windows3.1没有状态栏，作者甚至询问了chatgpt如何解决，但是chatgpt并没有给出正确的答案。
![](wingpt2.png)
- 用windows上的画图工具画了一个logo。
![](wingpt3.png)
- 为了解决内存限制问题，作者将问答的文本数量进行了控制，同时也不去发送上下文内容，这意味着wingpt无法根据前文进行问答。

作者放出了wingpt的[下载地址](https://www.dialup.net/wingpt/download/wingpt10.zip)和[源码](https://www.dialup.net/wingpt/tls.html)。（国外的hacker真是厚道）当然也有限制，必须运行在16位或者32位的windows3.1上，而且需要你有一个openai的api key。

### [XML是未来](https://www.bitecode.dev/p/hype-cycles)

好吧，这是一个标题党。文章很棒，作者的主要目的是吐槽前端技术的爆炸、微服务过度等许多愚蠢的潮流。

作者初次接触xml的时候，便被告知xml将取代一切：html变为xhtml，验证用dtd，用xslt进行转换和展示，用soap进行通信。当然最后的结果大家都知道，xml并没有取代一切。

作者从中学到的教训是：没有银弹，所有工具都有其优缺点。而技术人员自认为自己是理性的，其实受到了炒作，营销和情绪的影响，甚至受其影响比起普通人更甚。

而xml只是许许多多潮流中的普通一员而已，作者吐槽了好几个技术潮流的起伏：

- MangoDB出现时，突然所有东西都要使用NoSQL。新手们不管三七二十一，就把数据一股脑放在了NoSQL中，然而NoSQL没有没有模式、没有一致性，没有完整性验证，这导致大量项目的失败。
- Node出现使得Javascript风靡一时，你必须前后端使用同一种语言，让一切都是异步的。但是javascript本身并不完善，没有命名空间，混乱的作用域，弱类型等等。导致需要用各种额外框架，CoffeeScript，然后是Babel、Webpack、TypeScript、React + JSX等等。
- 单页面应用（Single Page Application）变得流行，但是你需要在flux、redux、alt、reflux、flummox、fluxible、fluxxor、marty.js、fynx、MacFly、DeLorean.js、fluxify、fluxury、exim、fluxtore、Redx、fluxx等之间做出选择。
- 客户端突然需要所有数据了，于是GraphQL诞生了。这导致了服务端渲染的兴起，也就是所谓的带有额外步骤的CGI。结果证明，这增加了巨大的复杂性，导致了大量失败的项目和金钱的浪费。
- “一切都应该是微服务”的观点变得主流，每个小网站都应该有一个restful API的Docker容器，一个用于前端，一个用于数据库。然后不断增加层级。接着为了更好通信，显然需要一个消息队列，ZeroMQ、RabbitMQ……服务间要更好的交换格式，又有了Protobuf的gRPC。但是这只是让工作变得更加复杂，结果导致了云服务的诞生，你只需要托管然后购买这些服务就可以了，费用变成原来的10倍，但至少比你自己维护多10000倍的维护成本要好。
- 接着大数据开始流行，你要存储用户的所有行为，你的数据变得很庞大，即使不够大，你也得相信数据足够大。然后你需要某种形式的Dynamo数据湖，又或者是一个时间序列数据库等等。
- 程序运行变慢了？不是因为糟糕的程序设计，不是因为高并发的请求，而是因为语言！所以我们用GO和RUST来重写程序吧！

作者最后总结，你需要新技术，云, 容器, nosql, go, rust 以及 js 构建的系统等。**但是不需要每一项新技术**，没有什么东西适用于所有情况（Nothing is ever needed for everything）。

这篇博文的评论区一位老哥也说的很棒，现在人们评价一个人技术好坏并不是看他解决的问题有多复杂，而是看他设计的系统有多复杂，这显然不是一个好趋势。
![](hypecycle1.png)

### [mycelite：一个SQLite扩展，它允许你将一个SQLite实例中的更改同步到另一个实例。](https://mycelial.com/docs/get-started/quick-start/)


- 下载mycelite拓展
``` shell
curl -L https://github.com/mycelial/mycelite/releases/latest/download/aarch64-apple-darwin.tgz --output aarch64-apple-darwin.tgz
tar -xvzf aarch64-apple-darwin.tgz
```
- 配置SQLite写实例
  - 启动
  ```shell
  sqlite3
  ```
  - 加载插件
  ```shell
  .load ./libmycelite mycelite_writer
  ```
  - 打开一个数据库作为写实例
  ```shell
  .open writer.db
  ```
- 配置Mycelite：第一次使用Mycelite，需要进行加载和配置
  - 加载配置文件 
  ```shell
  .load ./libmycelite mycelite_config
  ```
  - 注册账号获取user name和secret https://hub.mycelial.com/signup
  - 执行插入语句，填入user name和secret
  ```sql
  insert into mycelite_config values 
    ('domain', '<domain>/<db-name>'),
    ('client_id', '<your-hub-username>'),
    ('secret', '<secret-from-hub>');
  ```
- 配置SQLite读实例
  - 启动
  ```shell
  sqlite3
  ```
  - 加载插件
  ```shell
  .load ./libmycelite mycelite_reader
  ```
  - 打开一个数据库作为读实例
  ```shell
  .open reader.db
  ```
- 验证同步
  - 在写实例执行插入语句
  ```sql
  create table test(number integer);
  insert into test(number) values (42);
  ```
  - 在读实例就能看到数据
  ```sql
  .tables
  select * from test;
  ```