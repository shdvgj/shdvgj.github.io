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
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2019/10/08/hexo使用技巧/1.png) 
点击图片进去可以看到这种主题的预览效果  
点击文字进去将进入这个主题的github站点, 里面会详细说明该主题的安装和配置方式  

2.目前最流行的hexo主题应该是NexT, 我这次选的是3-hexo作为例子来讲解一下, 这个也是一个优秀的国产hexo主题  
其最终效果如图  
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2019/10/08/hexo使用技巧/2.png)   

3.关于3-hexo的安装和设置方法, 都可以直接参照[该主题的github主页](https://github.com/yelog/hexo-theme-3-hexo)  
简单介绍一下:  
  1.    获取3-hexo: git clone https://github.com/yelog/hexo-theme-3-hexo.git themes/3-hexo  
  2.    将3-hexo的主题文件夹复制到hexo的themes目录下  
  3.    修改3-hexo目录下的_config.yml文件, 修改方式在_config.yml中已经有详细的注释, 按照注释修改即可  
  4.    hexo主目录下打开git窗口, 依次输入如下命令  
    hexo clean (清除静态页面缓存文件, 防止出现缓存影响页面样式的情况)  
    hexo g (生成静态页面)  
    hexo s (启动本地hexo服务器, 可以打开localhost:4000查看页面效果)  
    hexo d (发布到github)  


## 设置分类和标签  
1.每一个由hexo生成的md文件, 其内容开头都是由两个"---"组成的, hexo把这部分称为[Front-Matter](https://hexo.io/zh-cn/docs/front-matter), 如图:  
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2019/10/08/hexo使用技巧/3.png)  
 
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
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2019/10/08/hexo使用技巧/4.png)


## MarkDown开发工具  
我本人不太喜欢为markdown开发再安装一个软件, 所以以下都是针对程序员电脑上通常已有的软件上安装插件, 当然如果没有这些软件, 那推荐直接安装[typora](https://typora.io/)  

1.Notepad++插件MarkdownViewer++  
如果已经安装了Notepad++, 可以直接下载[MarkdownViewer++](https://github.com/nea/MarkdownViewerPlusPlus/releases)插件  
下载完成后, 直接将dll文件放入Notepad++主目录下的plugin文件夹下, 然后重新启动Notepad++即可, 在Notepad++的工具栏会出现一个"m"图标:  
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2019/10/08/hexo使用技巧/5.png)  
点击即可实时查看页面效果  
优点: 插件轻量级, 1MB不到, 占用系统的内存消耗极小  
缺点: 功能非常有限, 只能实时查看效果, 不能对markdown语法高亮  

2.Idea插件MarkDown Navigator
其实Idea自带了一个MarkDown的插件, 但是这个插件不太好用, 所以这里不再介绍了  
打开Idea, 打开File-Settings-Plugins-Browse Repositories, 查找MarkDown Navigator, 点击Install, 重新启动Idea即可  
效果如图:  
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2019/10/08/hexo使用技巧/6.png)  
优点: 支持MarkDown语法高亮, 支持语法快捷键, 且在Idea内开发, 对于程序员来说比较习惯  
缺点: 毕竟需要Idea来开发, 对于仅仅只要写博客的人来说, 显得有点笨重  

3.MarkDown在线编辑器  
在线编辑的好处不言自明, 不用安装任何软件和插件, 只要有浏览器就行
推荐https://www.mdeditor.com/ 

## 为博客设置密码  
1.安装插件: 在hexo主目录下执行npm install --save hexo-blog-encrypt  
2.在需要设置密码的博客的Front-Matter里面加一个参数  
password:*****
