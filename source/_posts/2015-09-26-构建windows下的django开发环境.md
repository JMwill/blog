title: 构建windows下的django开发环境
date: 2015-09-26 20:07:33
tags:
---

转回windows系统后，用于老师方面的项目还没有完结，因此需要在windows下进行django的开发。现在先记录一下windows下的环境搭建方法，留作记录，同时也给其他需要在windows下进行django开发的童鞋一些方便\*´∀\`)´∀\`)\*´∀\`)\*´∀\`).

<!-- more -->

***

## 安装python

在这里我选择的是官方的最新版本，自带有pip，如果是python 2.7.9或者3.4.0以下的版本就需要自己进行pip的安装，具体方法看[这里][install pip in windows]。python的[官网][python site]。安装python的时候记得需要进行path的添加，安装的时候有选项的，不过在最下面，需要拉一下滚动条。如果没有添加path的话就需要自己手动添加了，比较麻烦而已。

## 安装setuptools

可能有的童鞋已经安装了python的其他没有pip的版本，因此需要用setuptools来进行pip的安装，同时使用setuptools还可以进行**ipython**的安装，这里的ipython最好使用setuptools来进行安装，这样可以将相应的附加内容也一并安装，而在pip上并不会安装附加内容（（*≥ｍ≤*）这个我只是在别人的安装记录上看到的，没有验证，有兴趣的童鞋可以试一下）。

安装setuptools的方法是在[这里][setuptools downloads]下载安装脚本，然后运行，我这里具体是直接双击，因为python已经加入路径，所以脚本可以直接运行。到这里setuptools就已经安装完成了。到这里，没有pip的童鞋可以在[这里][pip downloads]进行pip的下载，下载完成后用[7-zip][7-zip downloads]进行解压（其他也可以喇），然后用cmd进入文件夹运行找到**setup.py**并运行它`python setup.py install`。到这里没有pip的童鞋也有pip可以用了。

## 安装ipython

在这里我推荐安装ipython，它比python自带shell模式好用很多，安装的方法在这里就很简单了直接在cmd上输入`easy_install ipython`，如果出错，有可能是原python的Scripts/路径没有添加，或者网络不稳定的原因，具体看出错信息，自行解决吧。

## 安装virtualenv

由于已经具有pip这一个工具，这里就可以使用pip来进行virtualenv的安装`pip install virtualenv`，等待安装完成后，创建一个文件夹用于存放virtualenv创建的虚拟环境，在cmd中进入到创建的文件夹，然后输入`virtualenv --no-site-packages [name you prefer]`。

创建完成自己的虚拟环境之后就可以通过activate脚本来运行虚拟环境了。在我这里是`C:\Users\JMwill\virtualenvs_of_will\willenv\Scripts\activate`，输入后回车，就会看见C盘符的前面带着虚拟环境的名字了。之后使用pip安装的包都会安装在虚拟环境下。这样就不用担心原python环境被搞得一团糟了。

如果想要退出虚拟环境，将**activate**换成**deactivate**回车就行。

## 让ipython支持env

按照安装的资料上说之前安装好的ipython默认是不会去查找env内的包的，这样在ipython中就无法引入相应的包。当然，你也可以在env内再一次安装ipython，但是这不是一个好的处理方法。所以，在这里需要对ipython进行一下小修改。但是，在我这里ipython并没有这种情况出现，但是，还是在这里将解决办法提供一下。

在cmd中输入`ipython prefile create`，生成的文件名称应该是ipython_config.py。文件应该是存在ipython的profile文件夹内，windows中应该是[你的用户文件夹]\\.ipython\profile_default这里。

打开ipython_config.py文件的最后输入以下页面中的代码[这里][ipython code one]，参考[这里][ipython code two]，还有[这里][ipython code three]进行微调。到这里，再次启动ipython，注意输出信息是否有`VIRTUAL_ENV -> c:\[你的虚拟环境路径]\Lib\site-packages`这样的信息。

## 安装Django

到这里，终于要安装Django框架了。进入或者创建一个虚拟环境，然后运行虚拟环境。再在cmd中输入`pip install django`，安装完成后可以使用ipython进行检查，输入`import django;django.VERSION`，检查完毕后就可以开始创建一个django项目来玩玩了。

`python -m django-admin startproject [projectname]` -m参数是告诉python寻找django-admin模块，记得不要直接使用django-admin来创建新项目哦，输入后回车应该就可以在当前目录下创建一个django项目了。

## 安装MySQL

接下来进行MySQL[下载][MySQL downloads]以及安装，下载这里，可以选择下载一个完整的mysql，我这里硬盘肚量不够（┬＿┬），无奈只好下载一个server版本足够使用就算了。安装完成后记得检查是否将mysql添加到了path里面，打开cmd输入`mysql --version`，可以查看到mysql的版本，这样mysql server就安装完毕了。到这里，可以创建一个用于项目的用户，或者创建一个用于项目的数据库。

`----------------------------------我是分割线2015-10-14-----------------------`

这段时间需要在其他地方重新搭建一个django的运行环境，MySQL的安装的部分使用如果使用zip包的话还要自己配置路径、新增用户等的一些操作，过程比较麻烦，在这边还是推荐使用集成好的[wamp][wamp server]来进行MySQL的安装，wamp中还有好用的PHPmyadmin和Apache、有时间还可以玩玩夹带的PHP。在这里需要注意的是，wamp下载可能需要用到梯子，如果发现点击下载链接没有反应，那就需要翻一下墙，而且wamp安装好之后默认root是没有密码的，如果只是测试的话，可以不设定，但最好还是设定一下密码。


## 安装MySQL-python

在[这里][MySQL-python downloads]可以找到MySQL-python的下载链接，记得要看清对应的版本。下载完成之后不要立即启动安装程序，如果直接安装，安装到的地方是源python的安装目录下，这样python的虚拟环境是无法使用的。因此，我们需要先运行虚拟环境，然后使用easy\_install来安装。在cmd内进入到软件的下载目录下输入`easy_install mysql-python-1.*.*...`，*...*指的是你的软件名称，或者你也可以使用绝对路径来安装`file://...`。

到这里Django环境就基本安装好了，

[这里][raw article]是原文链接


[install pip in windows]:           http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows
[python site]:                      https://www.python.org/downloads/
[setuptools downloads]:             https://pypi.python.org/pypi/setuptools#windows-simplified
[pip downloads]:                    https://pypi.python.org/pypi/setuptools#windows-simplified
[7-zip downloads]:                  http://www.7-zip.org/
[ipython code one]:                 https://gist.github.com/bsweger/1176035
[ipython code two]:                 http://blog.algarvio.me/2009/1/29/ipython-and-virtualenv/
[ipython code three]:               https://code.google.com/p/modwsgi/wiki/VirtualEnvironments
[MySQL downloads]:                  http://dev.mysql.com/downloads/mysql/
[MySQL-python downloads]:           http://www.codegood.com/archives/category/python
[raw article]:                      http://www.swegler.com/becky/blog/2011/08/27/python-django-mysql-on-windows-7-part-i-getting-started/
[wamp server]:                      http://www.wampserver.com/en/