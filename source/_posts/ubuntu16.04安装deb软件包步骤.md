---
title: ubuntu16.04安装deb软件包步骤
date: 2016-09-19 15:00:58
categories: Linux
tags: 
  - ubuntu
  - .deb
  - apt-get
  - dpkg
---
## 前言

一直以来在OS X环境下写代码，忽然想起自己还安装了一个Linux发行版ubuntu 14.04，近日升级到ubuntu16.04系统后，却一度卡在了一些图形界面软件的安装上，解决之后寻思还是记录一下以供参考吧，水平有限，求大神勿喷。

像之前一样，从官网上下载到deb软件包，然后双击打开，会自动跳转到`ubuntu软件`界面，但这次好像有些不一样，无论如何点击“安装”，都无法顺利成功安装。好吧，当时确实有些懵了，后来从命令行的方式上尝试一下安装，才知道是由于缺少一些依赖导致的。
<!--more-->
![](http://ww4.sinaimg.cn/large/873fcdb7jw1f87mas01elj212i0x6tgi.jpg)

## deb软件包

deb包是debian，ubuntu等Linux发行版的软件安装包，是类似于rpm的软件包，而非debian,ubuntu系统不推荐使用deb软件包，因为要解决软件包依赖问题，安装也比较麻烦。

## 步骤

第一步当然是去相应的官网下载对应的Linux版本安装包啦，今天我们只讨论deb包的情况。

双击打开deb软件包，会自动跳到`ubuntu软件`，但这里我们遇到了无法直接双击安装啦。

*顺便提一句，Ubuntu 软件中心已在 Ubuntu 16.04 中被取消，取而代之的是 GNOME Software。「软件」是一个外观更现代、浏览更迅捷的商店应用，我们可以使用它来安装和更新应用程序。*

打开终端，这里以安装“网易云音乐ubuntu16.04（64位）为例，进入deb包所在的目录

```bash
$ ls
netease-cloud-music.deb
```

输入指令和密码：

```bash
$ sudo dpkg -i netease-cloud-music.deb
[sudo] tc 的密码：
```

之后就出现了下面的情形：

```bahs
正在选中未选择的软件包 netease-cloud-music。
(正在读取数据库 ... 系统当前共安装有 178887 个文件和目录。)
正准备解包 netease-cloud-music.deb  ...
正在解包 netease-cloud-music (1.0.0) ...
dpkg: 依赖关系问题使得 netease-cloud-music 的配置工作不能继续：
 netease-cloud-music 依赖于 libqt5x11extras5 (>= 5.1.0)；然而：
  未安装软件包 libqt5x11extras5。
 netease-cloud-music 依赖于 gstreamer1.0-plugins-bad；然而：
  未安装软件包 gstreamer1.0-plugins-bad。
 netease-cloud-music 依赖于 gstreamer1.0-plugins-ugly；然而：
  未安装软件包 gstreamer1.0-plugins-ugly。
 netease-cloud-music 依赖于 libqt5multimedia5-plugins；然而：
  未安装软件包 libqt5multimedia5-plugins。
 netease-cloud-music 依赖于 libqt5multimediawidgets5；然而：
  未安装软件包 libqt5multimediawidgets5。
 netease-cloud-music 依赖于 libqt5libqgtk2；然而：
  未安装软件包 libqt5libqgtk2。

dpkg: 处理软件包 netease-cloud-music (--install)时出错：
 依赖关系问题 - 仍未被配置
正在处理用于 hicolor-icon-theme (0.15-0ubuntu1) 的触发器 ...
正在处理用于 gnome-menus (3.13.3-6ubuntu3) 的触发器 ...
正在处理用于 desktop-file-utils (0.22-1ubuntu5) 的触发器 ...
正在处理用于 bamfdaemon (0.5.3~bzr0+16.04.20160415-0ubuntu1) 的触发器 ...
Rebuilding /usr/share/applications/bamf-2.index...
正在处理用于 mime-support (3.59ubuntu1) 的触发器 ...
在处理时有错误发生：
 netease-cloud-music
```

好吧，真相大白了，是缺少一些依赖关系的缘故，我们需要运行下面的指令来修正上面的错误：

```bash
$ sudo apt-get -f install
```

接下来就是静候上述指令修复依赖关系和自动安装一些依赖。等提示符正确返回后，再执行下面的指令安装deb软件包：

```bash
$ sudo dpkg -i netease-cloud-music.deb
```

等指令执行完成后，deb软件就已经被安装好了。
*如果仍然未安装成功，此时可尝试重启后再执行安装，因为一些修复的依赖可能需要重启后才能生效。*



这里其实就是两个指令，前一个是修复依赖关系和自动下载安装所需的依赖，后一个是dpkg套件管理系统指令，下面来简单介绍一下后者：

deb是debian linus的安装格式，跟red hat的rpm非常相似，最基本的安装命令是：

```bash
$ sudo dpkg -i package.deb
```

dpkg是Debian Package的简写，是为Debian专门开发的套件管理系统，方便软件的安装、更新及移除。所有源自Debian的Linux发行版都使用dpkg，例如Ubuntu、Knoppix等。



以下是一些 dpkg 的其他用法：

1.查看package.deb软件包中包含的文件结构（其中-c等价于\--contents）：

```bash
$ sudo dpkg -c <package.deb>
```

2.查看package.deb软件包的详细信息，包括软件名称、版本以及大小等（其中-I等价于\--info）：

```bash
$ sudo dpkg -I <package.deb>
```

3.移除一个已安装的包裹（软件名称可通过dpkg -I命令查看，其中-r等价于\--remove）：

```bash
$ sudo dpkg -r <package.deb>
```

4.完全清除一个已安装的包裹。和 remove 不同的是，remove 只是删掉数据和可执行文件，purge 另外还删除所有的配置文件，其中-P等价于\--purge：

```bash
$ sudo dpkg -P <package.deb>
```

5.查看package.deb软件包安装的所有文件（软件名称可通过dpkg -I命令查看，其中-L等价于\--listfiles）：

```bash
$ sudo dpkg -L <package.deb>
```

6.查看package.deb软件包的详细信息（软件名称可通过dpkg -I命令查看，其中-s等价于\--status）：

```bash
$ sudo dpkg -s <package.deb>
```

7.重新配置一个已经安装的包裹：

```bash
$ sudo dpkg-reconfigure <package.deb>
```
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=0 height=0 src="http://music.163.com/outchain/player?type=2&id=25638810&auto=0&height=66"></iframe>
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="https://music.163.com/outchain/player?type=2&id=25638810&auto=1&height=66"></iframe>

>最近访客

<div class="ds-recent-visitors" data-num-items="28" data-avatar-size="42" id="ds-recent-visitors"></div>
<br>