title: 创建舒服的Ubuntu开发环境
date: 2015-09-13 15:40:26
tags:
---

因为office的强大覆盖范围，我又无奈地投入到了windows的怀抱。但是之前使用Linux时手指在键盘上点击如飞的感觉实在是爽，于是，我又贱贱地装回了虚拟机来使用Ubuntu，不要问我为什么不装双系统 (ಥ \_ ಥ)，谁叫我手贱买了个120G的固态硬盘，又不想加装个机械硬盘呢。没地方分啊\_(:з」∠)\_。好了，那装回了Ubuntu系统之后，当然是尽量配置一个自己喜欢的环境了。这里只是列出了我自己喜欢的环境，以作记录。

<!-- more -->

***

## 材料准备

* [Ubuntu系统一枚][Ubuntu desktop]；
* 翻墙软件一枚；
* 已点翻墙天赋；（想要点翻墙技能可以点击[这里][will email]
* 装备VMware软甲；（[软甲在此][VMware]）
* 较为熟悉命令行操作；（步步高点读机，不会就点[这里][Command Line]）

好了，材料就先准备这些先，之后会慢慢补充进去的。

***

## 搭建python环境

因为我是一只程序猿，所以，一个舒适的文档编辑环境对我来说是非常重要的事情，在Ubuntu下，使用控制台进行日常开发是很舒适的，因此，这里我选择的编辑器就是vim，一个被誉为神之编辑器的编辑器，但是，在进行编辑器的全副安装之前，因为部分资源有可能需要搬（fan）梯（qiang）子，而搬梯子之前又需要pip，所以，第一步，先搭建较为舒服的python开发环境。由于最近的Ubuntu都有安装python的两个版本，因此，这里我们就省略安装python3的步骤先。

pip的许多包都需要libssl、以及libevent的编译环境，所以先安装：`sudo apt-get install build-essential libssl-dev libevent-dev libjpeg-dev libxml2-dev libxslt-dev`

安装完成后就进行pip以及python中十分好用的virtualenv的安装：

```
#安装pip
sudo apt-get install python-pip

#安装virtualenv
sudo pip install virtualenv
```

仅仅安装完当然是不行的，为了避免我们每一次都需要启动virtualenv来进行pip包的下载，我们直接默认启动virtualenv。先创建virtualenv环境：

```
# *号的位置为版本号，python有2.5,2.7,3.4,3.5等的版本
# 记得将*号换成相对应的版本号。
# 安装 python2.* virtualenv
virtualenv --no-site-packages -p /usr/bin/python2.* ~/.venv/python2.*

# 安装 python3.* virtualenv
virtualenv --no-site-packages -p /usr/bin/python3.* ~/.venv/python3.*
```

然后将代码添加到`~/.bashrc`的最后面，这里的意思是在启动控制台时就默认用virtualenv环境替代系统的环境。

```
# 缺省激活python2.7环境，这里为默认2.7的环境
if [ -f ~/.venv/python2.7/bin/activate ]; then
    . ~/.venv/python2.7/bin/activate
fi
```

到这里，一个较好的python开发环境就好了，之后就不怕弄坏系统的python咯。

## 搭梯子

在搭建好这个开发环境之后，就可以使用pip来下载梯子了，`pip install shadowsocks`，然后在你喜欢的地方进行shadowsocks的配置文件的编写：

```
# 可以在这一个文件夹下进行安装/etc/shadowsocks/config.json
{
    "server":"remote-shadowsocks-server-ip-addr",
    "server_port":443,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"your-passwd",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open":false,
    "workers":1
}

具体参数代表的意思：
server          服务端监听地址(IPv4或IPv6)
server_port     服务端端口，一般为443
local_address   本地监听地址，缺省为127.0.0.1
local_port      本地监听端口，一般为1080
password        用以加密的密匙
timeout         超时时间（秒）
method          加密方法，默认的table是一种不安全的加密，此处首推aes-256-cfb
fast_open       是否启用TCP-Fast-Open
wokers          worker数量，如果不理解含义请不要改
```

各项填写完整之后，就可以使用`sslocal -c /etc/shadowsocks/config.json`来运行客户端了，其他具体的问题，就需要自行解决了。都说用Linux系统的人都是喜欢折腾的人，感觉好像是的\_(:з」∠)\_，于是，在这里提供一个GUI形式的方便各位使用：

```
# 在进行gui的源添加后进行update时可能会出现hash校验和不符的情况
# 这时服务器商的缓存造成的，遇到这种情况可以使用这个方法：
# http://dockone.io/article/366，在这里我推荐换协议，毕竟，
# 这时候我们还没有成功搬到梯子，无法使用代理方法。

sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```
各位在Ubuntu下进行源的添加，然后安装完后就可以自动设置参数来使用,有没有windows下的方便感。ヽ(･∀･)メ，其他各个版本的安装方法在[这里][shadowsocks]

## 安装Git

在梯子搭建后之后，我们还需要现在炙手可热的版本管理工具Git的帮助，这个工具能够，让我们对自己的程序，工程，文件等进行版本管理。而且在编辑器添加功能的时候还是需要它的。下面我们先来安装Git：

```
# Git安装
sudo apt-get install git

# 安装完成后的配置工作
# 进行常用的命令设置alias
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.co checkout
git config --global alias.st status

# 更好地显示git log，加不加无所谓
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen (%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --"

# 设置用户信息
git config --global user.name "Your Name"
git config --global user.email you@email.com

# 缺省使用颜色显示，可加可不加。
git config --global color.ui true
```

为了让我们能够更好使用Git，在这里，我推荐安装[git-flow][git-flow]这个分支模型，使用方法在[这里][git-flow-usage]，好，接下来是安装方法：`sudo apt-get install git-flow`，就这样，我们安装好了这个好用的版本管理工具了。具体的使用方法等，可以在以后慢慢熟悉。

## 安装编辑器

好了，梯子搭好了（其实可以不用，不过有了就可以在需要时用上），版本管理工具（这个是一定要有）也装好了，现在我们可以进行编辑器的安装工作了\(ρ\_・).。\_\)，首先安装vim：`sudo apt-get install vim vim-gtk`，在这里需要安装`vim-gtk`，具体原因是在之后安装编辑器的时候需要用到gtk附带的一些功能。

在安装完成vim后，我们就可以安装编辑器的插件，一般使用的是Vundle来进行管理，但是，在这里我比较推荐的是使用别人写好的脚本来进行编辑器的配置，我使用的是[spf13][spf13-link]。在控制台内输入`curl https://j.mp/spf13-vim3 -L > spf13-vim.sh && sh spf13-vim.sh`即可进行spf13的下载，其会自动执行编译安装等环节。等待安装完成就可以。这样，一个较为舒适的Ubuntu下编辑环境就好了，

最后，个人推荐在Ubuntu等Linux环境下使用 **tmux**，这是一个优秀的终端复用软件，好处以及使用方法可以自行man或者google。直接`sudo apt-get install tmux`进行下载就可以了。在这里需要记住的就是，使用tmux时，默认使用颜色集为8，类似spf13中下载了多种主题的插件，其颜色就会变得不一样，使得难以使用，这使因为主题需要的颜色集是256，因此，在启动tmux时可以加上`-2`参数，即：`tmux -2`，这样就可以让tmux以256颜色集来启动终端。于是，又可以愉快地使用vim编辑器了。

## 结语

其实我一直很懒，每次都是临时在网上找资料来进行环境的配置，不过，总会忘了一些，这时候反而会更麻烦，因此就写了这样的一篇东抄西抄的东东出来，O__O "…"。还有，最后再说一下，我喜欢在控制台用黑底绿字的颜色配置，有没有黑（屌）客（丝）\_(:з」∠)\_的感觉，

参考链接：[开发舒适的python开发环境][soft-python-env]

[Ubuntu desktop]:              http://www.ubuntu.org.cn/download/desktop
[will email]:                  mailto://787505998@qq.com
[VMware]:                      https://my.vmware.com/cn/web/vmware/downloads
[Command Line]:                http://billie66.github.io/TLCL/
[shadowsocks]:                 https://github.com/librehat/shadowsocks-qt5/wiki/Installation
[git-flow]:                    http://nvie.com/posts/a-successful-git-branching-model/
[git-flow-usage]:              http://jeffkreeftmeijer.com/2010/why-arent-you-using-git-flow/
[spf13-link]:                  https://github.com/spf13/spf13-vim
[soft-python-env]:             http://blog.csdn.net/kingppy/article/details/13080919
