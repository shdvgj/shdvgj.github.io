---
title: 利用hexo和github搭建个人博客
date: 2019-10-08 08:55:33
tags:
---
## github建立个人网站  
1.github的账号申请不再赘述  
2.打开github.com, 点击new, 新建一个项目  
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2019/10/08/利用hexo和github搭建个人博客/1.png)  
3.输入项目名称, 注意项目名称的格式必须是"用户名.github.io"的格式
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2019/10/08/利用hexo和github搭建个人博客/2.png)  
4.点击Create Repository完成项目新建  
5.点击新建的项目, 进入settings, 往下找到GitHub Pages, 点击Choose a theme, 选择一个主题后, 点击select theme就完成了网站的发布  
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2019/10/08/利用hexo和github搭建个人博客/3.png)  
6.在浏览器输入"用户名.github.io", 就可以看到github为你默认创建的首页  

## hexo搭建博客  
1.在使用hexo搭建博客之前, 需要完成几个基础工作  
[安装git](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git)  
[新增ssh key到github](https://help.github.com/cn/articles/adding-a-new-ssh-key-to-your-github-account)  
[安装node.js](http://www.runoob.com/nodejs/nodejs-install-setup.html)  
2.在本地拉取github上刚刚创建的网页项目(用户名.github.io)
3.在项目目录下新建一个文件夹, 可以随意命名, 比如hexo  
4.进入hexo目录, windows下右键空白处, 点击Git Bash Here  
5.依次执行命令  
npm install hexo-cli -g  (安装hexo)  
hexo init  (初始化网站)  
npm install hexo-deployer-git --save  (安装hexo发布到git的插件)  
中间遇到需要确认的信息, 直接按回车即可  
6.打开hexo目录下的_config.yml文件, 有几个地方可以进行修改  
title: 你的网站名字  
author: 网站作者  
theme: 去[hexo主题](https://hexo.io/themes/)可以选择, 注意要先将主题下载到hexo/themes下才可选择该主题  
注意: **必须要修改的部分是deploy, 改成如下格式**  
```
deploy:
  type: git
  repo: git@github.com:yourname/yourname.github.io.git
  branch: master
```
其他配置的详细说明在[hexo配置文件说明](https://hexo.io/docs/configuration.html)  
7.接下来就可以开始写博客了  
使用命令hexo n "博客标题", 即完成了一个新的博客文件的创建  
该命令会在hexo/source/_posts目录下新建一个"博客标题.md"的文件  
该文件内容格式如下  
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2019/10/08/利用hexo和github搭建个人博客/4.png)  
正文内容使用markdown语法进行编写, 至于markdown怎么写, 可以参考[markdown官方文档](http://www.markdown.cn/)  
8.博客写完之后, 保存文件, 然后依次执行如下命令  
hexo g  (根据md文件创建静态网页文件, 注意每次更新博客内容都需要执行这个命令)  
hexo s  (这一步是启动本地hexo服务器, 主要是为了本地预览博客效果, 浏览器打开localhost:4000预览效果)  
hexo d (发布网页到github)  
9.浏览器打开"用户名.github.io"即可看到你刚刚写的博客内容  
![](https://raw.githubusercontent.com/shdvgj/shdvgj.github.io/master/2019/10/08/利用hexo和github搭建个人博客/5.png)  
## 小结  
总的来说, hexo+github搭建博客非常的简便, hexo内置的几种主题也都非常的简洁美观, 对于既想要有逼格的有自己的个人博客网站, 又不想太麻烦的人来说, 是个不错的选择

