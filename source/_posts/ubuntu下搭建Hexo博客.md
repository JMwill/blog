title: "ubuntu下搭建Hexo博客"
date: 2015-03-17 20:39:32
tags:
    - Ubuntu
    - Hexo
---
## ubuntu上制作Hexo博客


现在这个国内氛围里，我们什么都要求最低成本，所以咯，我们现在就搭建个免费的博客来使用一下下吧！(￣▽￣)~*

[hexo](http://hexo.io/)是一个基于Node.js的静态博客程序，可以方便的生成静态网页托管在github上。其作者是来自台湾的**@tommy351**。我们搭建**hexo**这个东东，是拿做博客的嘛，那想要找个地方来放我们写下的文章，应该要放到哪里呢，答案当然是大热的github上啦。所以我们需要先装上git和node.js。因此，我们又可以趁机来学一下node.js了。不多说，开工。
<!--more-->

***

## 1. Git安装
ubuntu上安装git只需要执行下面的命令：
```
sudo apt-get install git
```
为使用Github Page搭建博客, 我们需要在github上建立仓库,仓库名为**Github用户.github.io**，这样子我们才能通过**http://Github用户.github.io**访问到自己的博客。（关于git与github的操作配置，请看官网[github官网](https://github.com)

## 2. Node.js安装
因为hexo官网推荐使用nvm来安装node.js，所以我们可以使用下面这条命令安装**nvm**：

`$ wget -qO- https://raw.github.com/creationix/nvm/master/install.sh | sh`

然后来安装node.js的包管理器[nvm](http://npmjs.org/):

`$ nvm install 0.12 #目前官方最新版是0.12.X,按照实际情况修改`

安装完npm后，测试一下是否存在：

`$ npm -v`

## 3. 安装并初始化Hexo
安装完node.js跟git后，使用npm安装将要使用的Hexo：

`$ npm install hexo -g`

当安装完Hexo后在自己本地创建一个目录，在目录内运行：

`$ hexo init`

运行：

`$ hexo server或者hexo s`

就可以在本地[http://localhost:4000/](http://localhost:4000/)上看到效果了

接下来的操作可以自行官网学习（写文章时，如果不想博文在首页全部显示, 并能出现阅读全文按钮效果, 记得在你想在首页显示的部分下添加'&lt;!--more--&gt;'），下面说一说配置。

## 配置_config.yml
当初始化完成hexo之后，需要修改目录下的**_config.yml**文件下的**deploy**项：
```
deploy:
    type: git
    repo: git@github.com:github用户名/创建仓库.git.io.git #就是部署仓库的SSH,当然要记得上传ssh公钥到github啦。
    branch: master
    message: 随便写
```

***

教程初步完成，现在应该能够在github上看到自己的博客啦。ps：换个好看好玩的主题吧，慢慢玩！
