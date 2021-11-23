---
title: hexo使用技巧
date: 2019-10-08 16:07:43
tags: 
- hexo
comments: true
categories: 
- 学习
- 搭建相关
---
## 更换主题  
1.进入https://hexo.io/themes/ 可以选择多种主题  
![](1.png) 
点击图片进去可以看到这种主题的预览效果  
点击文字进去将进入这个主题的github站点, 里面会详细说明该主题的安装和配置方式  

2.目前最流行的hexo主题应该是NexT, 我这次选的是3-hexo作为例子来讲解一下, 这个也是一个优秀的国产hexo主题  
其最终效果如图  
![](2.png)   

3.关于3-hexo的安装和设置方法, 都可以直接参照[该主题的github主页](https://github.com/yelog/hexo-theme-3-hexo)  
简单介绍一下:  
  - 获取3-hexo: git clone https://github.com/yelog/hexo-theme-3-hexo.git themes/3-hexo  
  - 将3-hexo的主题文件夹复制到hexo的themes目录下  
  - 修改3-hexo目录下的_config.yml文件, 修改方式在_config.yml中已经有详细的注释, 按照注释修改即可  
  - hexo主目录下打开git窗口, 依次输入如下命令  
    hexo clean (清除静态页面缓存文件, 防止出现缓存影响页面样式的情况)  
    hexo g (生成静态页面)  
    hexo s (启动本地hexo服务器, 可以打开localhost:4000查看页面效果)  
    hexo d (发布到github)  
<!-- more -->

## 设置分类和标签  
1.每一个由hexo生成的md文件, 其内容开头都是由两个"---"组成的, hexo把这部分称为[Front-Matter](https://hexo.io/zh-cn/docs/front-matter), 如图:  
![](3.png)  
 
2.在Front-Matter的区域内可以设置关于文章的诸多信息, 包括标题, 创建时间, 标签, 目录, 是否开启评论等  

3.标签的参数名称为tags, 目录的参数名称为categories, 设置单个标签和目录的格式如下:  
```
tags: hexo
categories: hexo
```
设置多个标签和多级目录的格式如下:  
```
tags: 
- hexo
- blog
categories: 
- 学习
- 搭建相关
```
**注意:目录的先后顺序决定了其父子级关系, 如上面的例子中, "学习"目录是"搭建相关"目录的父目录. 在3-hexo主题中的效果如下:**  
![](4.png)

### 以下是我的hexo更新之后需要额外操作的步骤
4.使用命令创建tags和categories目录
 ```
 hexo new page tags
 hexo new page categories
 ```
这样会在source目录下生成tags和categories目录，并在该目录下生成index.md文件
5.在tags目录下index.md文件内增加一行 **type: tags**，如下
```
title: tags
date: 2021-11-23 08:46:12
type: tags
```
在categories.md文件内增加一行 **type: categories**，如下
```
title: categories
date: 2021-11-23 08:46:12
type: categories
```
6.重新执行hexo g即生成目录和标签

## MarkDown开发工具  
我本人不太喜欢为markdown开发再安装一个软件, 所以以下都是针对程序员电脑上通常已有的软件上安装插件, 当然如果没有这些软件, 那推荐直接安装[typora](https://typora.io/)  

1.Notepad++插件MarkdownViewer++  
如果已经安装了Notepad++, 可以直接下载[MarkdownViewer++](https://github.com/nea/MarkdownViewerPlusPlus/releases)插件  
下载完成后, 直接将dll文件放入Notepad++主目录下的plugin文件夹下, 然后重新启动Notepad++即可, 在Notepad++的工具栏会出现一个"m"图标:  
![](5.png)  
点击即可实时查看页面效果  
优点: 插件轻量级, 1MB不到, 占用系统的内存消耗极小  
缺点: 功能非常有限, 只能实时查看效果, 不能对markdown语法高亮  

2.Idea插件MarkDown Navigator
其实Idea自带了一个MarkDown的插件, 但是这个插件不太好用, 所以这里不再介绍了  
打开Idea, 打开File-Settings-Plugins-Browse Repositories, 查找MarkDown Navigator, 点击Install, 重新启动Idea即可  
效果如图:  
![](6.png)  
优点: 支持MarkDown语法高亮, 支持语法快捷键, 且在Idea内开发, 对于程序员来说比较习惯  
缺点: 毕竟需要Idea来开发, 对于仅仅只要写博客的人来说, 显得有点笨重  

3.MarkDown在线编辑器  
在线编辑的好处不言自明, 不用安装任何软件和插件, 只要有浏览器就行
推荐https://www.mdeditor.com/ 

## 为博客设置密码  
1.安装插件: 在hexo主目录下执行npm install --save hexo-blog-encrypt  
2.在需要设置密码的博客的Front-Matter里面加一个参数  
password:*****  

## 如何上传图片  
1.在github存储图片并在博客内关联图片链接  
这种方式比较推荐, 因为别的博客网站通常都会有防止外部访问的措施在, 存储
在github上的图片不会有外部无法访问的情况. 对于各个版本的hexo都能适用.
  - 打开hexo主目录下的_config.yml文件, 修改参数post_asset_folder为true
  - 使用hexo n "博客标题" 新建博客时将会同时新建一个同名的资源文件夹, 如图  
  ![](7.png)  
  - 将博客内用到的图片放置到资源文件夹内, 可以按顺序号命名, 如图  
  ![](8.png)
  - 在博客内链接该图片, 格式为 
  ```hexo
  ![](https://raw.githubusercontent.com/#username/#username.github.io/master/#YYYY/#MM/#DD/#title/#photoname)  
  #username : github用户名
  #YYYY/#MM/#DD : 如果没有修改_config.yml里的默认文件夹创建方式的话, 就是填入年月日即可, 如2019/10/14  
  #title : 文章标题  
  #photoname : 图片名称  
  ```
  比如我这篇博客的图片链接地址如下图  
  ![](9.png)  
  - 链接完图片后目前在博客的预览界面还无法看到图片, 需要先执行  
  hexo clean (非必要步骤, 如果博客页面格式显示不正确, 需要执行)  
  hexo g (创建博客静态网页文件)  
  hexo d (这一步用于发布博客, 也同时发布图片)  
  hexo d的命令将会把图片发布到github服务器上, 也就可以在上述格式的图片地址里看到该图片  
  
2.直接链接本地地址, 这种方式也很方便, 网上也有挺多人使用这种方式, 主要优点
是不用多操作一步链接网络地址, 我贴一下该方式:
  1. 打开hexo主目录下的_config.yml文件, 修改参数post_asset_folder为true
  2. 安装图片插件 npm install hexo-asset-image --save 
     或者安装  npm install hexo-renderer-marked -s
  3. 在博客内直接引用本地地址即可, 如  
  ``` hexo
  ![](/1.png)
  ```
## 设置首页文章摘要
有三种方法，推荐使用第一种，更加灵活的根据文章进行不同长度的摘要截断，而且对于不同的插件也有最好的适应性
1.在文章中使用 <!-- more --> 手动进行截断，Hexo 提供的方式
2.在文章的 front-matter 中添加 description，并提供文章摘录 
3.自动形成摘要，在 主题配置文件 中添加
```properties
auto_excerpt:
  enable: true
  length: 150
```
默认截取的长度为 150 字符，可以根据需要自行设定