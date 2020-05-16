+++
title = "WSL，Windows 与 Linux 的丝滑融合"
date = 2020-05-17T00:10:00+08:00
lastmod = 2020-05-17T00:11:49+08:00
draft = false
+++

偶然的机会，需要使用 Windows 来进行开发，由于我平时主要使用 MacOS 与 Linux 开发，所以在 Windows 上进行开发的时候也必不可少地需要使用命令行。一开始是只使用系统的 CMD 来执行任务，但是 Windows 下的命令行与 Linux 下的有诸多不同，使用起来束手束脚，十分不方便，即使是安装了 [Cygwin](https://www.cygwin.com/) 或者 [cmder](https://cmder.net/)，使用起来还是很别扭。

总的来说，以上的两个工具日常开发是勉强够用的，毕竟就只是运行一下命令。但是，如果已经习惯 Linux 下顺畅的命令行使用体验，且想要在 Windows 上也能实现的话在以前来说需要付出的劳动可不小，还不能保证稳定。不过随着 Windows 推出的 Windows Subsystem for Linux 简称：WSL 功能后，你可以在 Windows 上将 Linux 作为一个应用一样打开使用，在 WSL 中使用命令行开发，必要的时候可以直接使用 Windows 提供的工具。这一刻，Windows 终于放下身段开始与 Linux 交融起来了。如此一来，以 Windows 作为日常开发的操作系统在我看来也不是不可以接受的了。


## 引入 WSL {#引入-wsl}

上面说到 WSL 有多么好，但再好，也只有在尝试后才能确定是否是自己的菜，具体的安装方法可以按照官方提供的操作：[【官方】安装 WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10)，喜欢尝鲜的可以看这里：[【官方】安装 WSL 2](https://docs.microsoft.com/en-us/windows/wsl/wsl2-install)，值得一提的是许多公司内部的网络对访问的站点进行了自定义的限制，会导致 Windows 的更新以及获取 WSL 安装包的过程受阻，如果遇到无法在应用商店里下载 WSL 运行包的情况的话可以参照：[【官方】WSL 离线安装](https://docs.microsoft.com/en-us/windows/wsl/install-manual)，下载下来的安装包甚至可以直接双击安装，也是很便捷的一种方式。

安装完 WSL 后，通过呼出开始窗口，输入（假设安装的是 Ubuntu）：Ubuntu ，弹出的窗口里面运行的就是 Ubuntu 版的 WSL。

到此，一个 Windows 下的 Linux 子系统就好了，基本上 Linux 支持的都可以在这个子系统里面做。但是，如果仅仅是要运行个子系统的话，直接用虚拟机 VMware 之类的早就可以做到。确实，如果只是要用一个 Linux 系统，虚拟机就可以包办。不过讲到融合，虚拟机暂时是追不上的。因为我主要做 Web 开发，下面就介绍我的一些开发配置作为参考，其他的大家可以按照需要发掘。


## 配置环境 {#配置环境}


### 串联 Linux 与 Windows {#串联-linux-与-windows}

在 WSL 启动后，除了可以使用常用的 Linux 命令，还有一个十分有用的特定需要用起来，那就是： ****调用 Windows 命令**** ，如何调用呢？直接输入命令名称。例如：

```sh
# 在浏览文件夹的时候突然想要在 Windows 文件夹中查看或者预览
explore.exe ~/folder/you/want/to/open/
# 或者
explore.exe ~/file/you/want/to/open.txt

# 当你需要复制文件中的文字时
cat ~/file/you/want.txt | clip.exe
```

上面的例子通过组合 Linux 以及 Windows 命令，整个开发体验直线上升。

我个人并不是直接使用 Windows 的命令而是将它 alias 一下，如将 `explore.exe` 变为 `wopen` ，与 MacOS 以及 Linux 上的 `open` 命令对应起来。


### 安装 Visual Studio Code {#安装-visual-studio-code}

Web 开发少不了使用这个热门编辑器，在 Windows 上安装完后，通常我比较习惯在浏览文件夹时直接用 `code` 命令来打开项目文件夹。一开始使用 WSL 的时候还以为不可以这样做了，很是苦恼了一阵子。后面偶然的机会了解到 WSL 以及 VSCode 的开发人员们已经处理好了这个问题，在 Windows 上安装完后直接在 WSL 上输入 `code` 指令就会触发安装需要的内容，完成后 VSCode 会打开，并需要安装插件支持，常用的开发插件也会要求你重装。安装的插件可以参考：[【官方】在 WSL 上开发](https://code.visualstudio.com/docs/remote/wsl)，这里的介绍比较完善，不过上面安装的插件应该换成是：[Remote - WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl)，可能是还没有更新到。

至此，Web 所需要的基本开发环境就完成了。不过，开发环境这种东西是永远配置不完的，只要一天在开发，就会不断地完善；就像厨师的刀具，不断地磨砺，直到顺手。


### 完善环境 {#完善环境}

在 Linux 之类的环境上开发的时候，我比较喜欢用 [Zsh](http://zsh.sourceforge.net/) 结合 [Oh My Zsh](https://ohmyz.sh/)，不过安装完后，如果使用一些主题的话，需要面临字体不合适的问题。如果使用的主题需要的字体找不到的话此时打开的界面将会十分丑陋

{{<figure src="/blog/ox-hugo/Yc5JLn_2020-05-16_21-20-36.png">}}

想要解决这个问题可以遵照以下的步骤：

1.  下载所需的字体，如：[Nerd Font](https://www.nerdfonts.com/font-downloads)
2.  遵照 [Windows 的指南](https://support.microsoft.com/zh-cn/help/314960/how-to-install-or-remove-a-font-in-windows)来将字体安装到系统中
3.  VSCode 需要进行配置，在 config.json 中添加： `"terminal.integrated.fontFamily": "主题需要的字体名称",`
4.  如果仅仅需要用 Shell 可以用 [这个方法](https://gist.github.com/romkatv/aa7a70fe656d8b655e3c324eb10f6a8b) 来修复主题字体问题，不过，最好的方式是在 PowerShell 的菜单栏右键，选择「属性-字体」修改默认字体为所需字体，或者使用喜欢的第三方 Shell

以上就是完善的设置，如果使用的是 fish shell 的话可以参考[这里](https://gist.github.com/nixin72/20dec757e021bb9e594c6f087acfe609)。

具体的开发过程中需要使用到的许多软件、工具就不在这里一一列出，实际开发的时候有需要再安装就可以。最后再推荐一下将键盘上的 CapsLock 键利用起来，这个键放在如此方便的位置却只是用作切换大小写实在是太浪费了。有兴趣的可以产考[这篇文章](https://www.jianshu.com/p/ab0b79713359)来设置。改键工具的文档在[这里](https://wyagd001.github.io/zh-cn/docs/Program.htm)


## 结尾 {#结尾}

上面就是这段时间使用 Windows 开发过程中总结下来的经验，期间自己的环境安装文件也出了许多问题，比如：[pip 的重命名](https://stackoverflow.com/questions/44455001/how-to-change-pip3-command-to-be-pip)，[pyenv 安装前依赖未确认](https://realpython.com/intro-to-pyenv/)等，都一并写在这里记录下来作为自己的[过程资产](https://wiki.mbalib.com/wiki/%25E7%25BB%2584%25E7%25BB%2587%25E8%25BF%2587%25E7%25A8%258B%25E8%25B5%2584%25E4%25BA%25A7)吧。