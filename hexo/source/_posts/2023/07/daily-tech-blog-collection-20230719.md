---
title: 每日文章收集-20230719
date: 2023-07-19 09:59:25
categories: 
- 每日文章收集
---

### [Stable Diffusion的一个网页DEMO](https://islamov.ai/stable-diffusion-webgpu/)

![](sd1.png)
这个网页使用的是你本地的内存和GPU来生成AI图片，根据网页介绍：
> 每个步骤大约需要1分钟+额外的约10秒来运行VAE解码器生成图像。打开DevTools会将所有操作减慢到大约2倍的速度。UNET仅在CPU上运行（速度快10%，在GPU上无法正确生成结果），因此会导致浏览器标签卡顿。
> 
> 为了得到可接受的结果，最少需要20个步骤。然而，对于演示目的，3个步骤就足够了。
> 
> 模型文件将被缓存，因此您无需每次下载它们。

另外需要最新版本的chrome，并打开`Experimental WebAssembly`和`Experimental WebAssembly JavaScript Promise Integration (JSPI)`的设置。

<!-- more -->

为了生成图像，这个网页还需要下载3.5gb的数据和耗费8gb的内存。

![](sd2.png)

### [lazydocker：管理Docker的懒人方式](https://github.com/jesseduffield/lazydocker)

![](lazydocker1.png)
这是一个简单的终端UI用于管理docker容器和镜像。

#### 前提

- Docker 版本 >= 1.13 (API >= 1.25)
- Docker-Compose 版本 >= 1.23.2 (optional)

#### 安装

有多种不同的[安装方法](https://github.com/jesseduffield/lazydocker#installation)，最通用的方法如下：
```sh
curl https://raw.githubusercontent.com/jesseduffield/lazydocker/master/scripts/install_update_linux.sh | bash
```

#### 用法

使用命令`echo "alias lzd='lazydocker'" >> ~/.zshrc`来展示lazydocker界面。

#### 主要feature
- 一目了然地查看Docker或Docker Compose容器环境的状态
- 查看容器/服务的日志
- 查看容器指标的ASCII图表，以便不仅感觉像开发人员，还能看起来像开发人员
- 自定义这些图表以测量几乎任何您想要的指标
- 附加到容器/服务
- 重新启动/删除/重建容器/服务
- 查看给定镜像的祖先层
- 清理占用磁盘空间的容器、镜像或卷

### [no-more-secrets：一个命令行工具，它可以重现1992年电影《Sneakers》中著名的数据解密效果。](https://github.com/bartobri/no-more-secrets)

就是类似于这样的特效：

![](sneakers1.gif)

- 下载和安装
  - 前提是安装了`git`, `gcc`, 和 `make`命令工具
  - 通过如下命令进行安装：
```sh
$ git clone https://github.com/bartobri/no-more-secrets.git
$ cd ./no-more-secrets
$ make nms
$ make sneakers             ## Optional
$ sudo make install
```
  - 卸载：`$ sudo make uninstall`
- 使用：`nms`是一个管道命令，可以输入任意字符来看到解密特效。以下命令是将目录输入到nms进行解密输出。
```sh
$ ls -l | nms
$ ls -l | nms -a           // Set auto-decrypt flag
$ ls -l | nms -s           // Set flag to mask space characters
$ ls -l | nms -f green     // Set foreground color to green
$ ls -l | nms -c           // Clear screen
$ nms -v                   // Display version
```
效果如下：
![](sneakers2.gif)


### [前端框架间最重要的区别](https://themer.dev/blog/the-single-most-important-factor-that-differentiates-front-end-frameworks)

在互联网上有大量关于不同框架之间差异及如何为您的下一个Web项目选择合适框架的博客文章。通常，这些文章会涵盖框架的一些方面，比如语法、开发设置和社区规模等。

而这篇只看一个点，就是`变量监测`，也就是看前端如何监测到变量的变化并立刻反映到页面上。

- React
> “I’ll manage state so that I know when it changes.”  - 我将管理状态，以便知道它何时发生变化。

React的变更检测范式很简单：应用程序状态由框架内部维护（同时向开发者提供API来更新状态），以便React知道何时重新渲染。
```js
export default function App() {
	const [count, setCount] = useState(0);
	return (
		<div>
			<button onClick={() => setCount(count - 1)}>decrement</button>
			<span>{count}</span>
			<button onClick={() => setCount(count + 1)}>increment</button>
			<button onClick={() => setTimeout(() => setCount(count + 1), 1000)}>increment later</button>
		</div>
	);
}
```

- Angular
> “I’ll make the developer do all the work.”  - 我会让开发者完成所有工作。

实际上，Angular使用NgZone来观察用户操作，并在每个事件上检查整个组件树。

对于任何相对较大的应用程序，这会导致性能问题，因为检查整个树很快变得成本过高。因此，Angular提供了一种逃生机制，允许开发者选择不同的变更检测策略：OnPush。OnPush意味着开发者需要通知Angular何时发生了状态变化，以便Angular重新渲染组件。除了默认的朴素策略外，OnPush是Angular提供的唯一其他变更检测策略。启用OnPush后，如果状态异步更新，我们必须手动告诉Angular的变更检测器检查新状态。

Angular的变更检测机制是一个灾难。开发者面临两个次优的选择：缓慢和朴素的默认实现，或者手动管理变更检测的复杂性。虽然Signals将会使其改进得多，但也感觉很慢。

- VUE
> "I’ll track changes to state and react accordingly." - 我将跟踪状态的变化并做出相应的反应。

Vue对变更检测的方法与React和Angular都有微妙的不同。与调用框架函数来改变状态（React）或改变状态并通知框架它已经被改变（Angular）不同，Vue使用被框架特殊设定的状态对象来进行变更的截取和检测。

Vue利用JavaScript语言特性，使开发者能够在处理有状态变量时无需考虑变更检测。

- Svelte
> "I’ll figure it out for you at compile time." - 我将在编译时为您找到解决方法。

Svelte对变更检测的方法完全是全新的。在编译时，Svelte分析组件代码的AST（抽象语法树），并在编译输出中注入一些代码，以在必要时对DOM进行精确的更新。

Svelte取得了一种罕见的双赢平衡：开发者完全无需考虑变更检测，可以直观地与有状态变量交互；同时，由于仅包含了最少量的应用程序（包含了内置的变更检测），用户体验得到了改善，因为这样的应用程序在浏览器中加载更加高效。

总结：每种方法都会对最终用户的应用程序性能产生影响。React、Vue和Angular都会向用户的浏览器提供需要解析和执行的运行时。而Svelte选择成为一个编译时框架，在大多数情况下消除了这种需要，从而使用户获得更快的加载体验。每个框架都有细微差别，使其更容易受到特定类型的错误（通常是围绕状态管理或变更检测），最终用户将会体验到这些影响。

找到适合应用程序需求的变更检测范式，一切就会顺利进行。选择不合适的范式，将在整个项目的生命周期内与之作斗争。
