---
title: Git指令小记
date: 2016-08-24 22:40:48
categories: Note
tags: 
  - Git
---

## 前言

`git`指令有很多，除了常用的git clone、git init、git add、git commit、git push，一些其他的如分支、回退等指令用到的频率不高，所以整理了一份`git`指令笔记以备遗忘时查阅


## 笔记

### git基本指令

`git init` 初始化一个git仓库
<!--more-->
`git add a.txt`  把a.txt 添加到stage（暂存）区    （不在暂存区的文件，git指令是不管的）

`git add .` 用来添加当前目录全部文件到暂存区

`git commit -m '我的第一行代码' ` 把暂存区内容添加到提交区，-m 参数后面引号内容为注释*（必须要有注释才能提交）*

`git status`用来查看git仓库的状态 

`git push` 用来把本地仓库推到远程仓库

`git push -f` 用来强制推送到远程，把远程的提交全部覆盖掉

`git pull` 用于拉取远程仓库的新提交到本地仓库

拓展：[git暂存区和工作区详解]([http://www.121ask.com/thread-5627-1.html) &nbsp; &nbsp;[git status详细参考说明](http://www.myexception.cn/software/1222764.html)

### zsh安装oh my zsh插件后的简化指令

`tab`键用来补全，调出补全项和切换补全项

`gss` 或者 `gst` 查看目录状态

`glog` 查看提交日志（ `q` 退出日志页面）

`ga .` 添加文件

`gco` 相当于`git checkout`

`gc -m` 相当于 `git commit -m '注释'`


### git checkout

`git checkout` 的主要功能就是迁出一个分支的特定版本，默认是迁出分支的head版本。

`git checkout master` 迁出master分支的head版本。

`git checkout testing` 切换到testing分支上，通过向该命令传递一个`-b`参数，可实现创建并切换分支的功能

`git checkout tag_name` 在当前分支上，取出 tag_name 的版本

`git checkout master file_name` 放弃当前对文件file_name的修改

`git checkout commit_id file_name` 取文件file_name的 在commit_id时的版本，commit_id为 git commit 时的sha值。


### git branch

`git branch` 列出本地分支，以*开头表示

`git branch -r` 列出远程仓库中的分支

`git branch -a` 列出远程与本地仓库的全部分支

```
$ git branch
* master

$ git branch -r
origin/master

$ git branch -a
* master
  remotes/origin/master
```
上面命令表示，本地主机的当前分支是`master`，远程分支是`origin/master`。

`git branch testing` 创建名为testing的新分支

`git merge hotfix` 将hotfix分支合并到当前分支当中去

`git branch -d testing` 删除名为testing的本地分支，`-d`选项只能删除已经被当前分支所合并过的分支，而要强制删除没有被合并过的分支，可以使用`-D`

`git branch -m oldbranch newbranch` 重命名分支，` -M`用来强制重命名，如`newbranch`已经存在时


### git clone
从远程主机克隆一个版本库，需要用到指令`git clone`
```
$ git clone https://github.com/jquery/jquery.git
```
上述命令表示，克隆了jQuery的版本库

### git remote

git会要求每个远程主机都要指定一个主机名，我们用`git remote`命令来管理主机名。 当指令后面不带选项的时候，`git remote`命令会列出所有远程主机。

```
$ git remote
origin

```

`git remote -v`可以查看远程主机的网址

`git clone`克隆版本库的时候，所使用的远程主机会自动被git命名为`origin`，如果想用其他的主机名，需要用`git clone`命令的`-o`选项指定。

```
$ git clone -o <其他的主机名> <url>
$ git remote
<其他的主机名>

```
上面命令表示，克隆的时候，指定远程主机叫做<其他的主机名>。

### git push
`git push`命令用于将本地分支的更新，推送到远程主机。

```
$ git push <远程主机名> <本地分支名>:<远程分支名> 

```
如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略。
```
$ git push origin
```
上面命令表示，将当前分支推送到origin主机的对应分支。

如果当前分支只有一个追踪分支，那么主机名都可以省略。
```
$ git push
```

### git fetch
在远程主机的版本库有了更新（commit）时，需要将这些更新取回本地，这用到git fetch命令。

```
$ git fetch <远程主机名>
```

将某个远程主机的更新，全部取回本地

```
$ git fetch <远程主机名> <分支名>
```
取回远程主机的某个特定分支的更新
```
$ git fetch origin master
```
取回origin主机的master分支


### git pull
`git pull`命令的作用是，取回远程主机某个分支的更新，再与本地的指定分支合并。

```
$ git pull <远程主机名> <远程分支名>:<本地分支名>
```

取回远程主机某个分支的更新，再与本地的某个分支合并
```
$ git pull origin next:master
//取回origin主机的next分支，与本地的master分支合并
```
如果远程分支是与当前分支合并，则冒号后面的部分可以省略。
```
$ git pull origin next
```
上面命令表示，取回`origin/next`分支，再与当前分支合并。实质上，这等同于先做`git fetch`，再做`git merge`。
```
$ git fetch origin
$ git merge origin/master
```


### git也允许手动建立追踪关系

```
$ git branch --set-upstream dev origin/master 
//指定dev分支追踪origin/master分支
```
若当前分支与远程分支存在追踪关系，`git pull`就可以省略远程分支名
```
$ git pull origin
```
上面命令表示，本地的当前分支自动与对应的origin主机"追踪分支"（remote-tracking branch）进行合并。

如果当前分支只有一个追踪分支，连远程主机名都可以省略。
```
$ git pull
```
上面命令表示，当前分支自动与唯一一个追踪分支进行合并。


### 忽略某个被追踪文件的修改

如果某些文件已经被跟踪了， 再添加到.gitinore时可能会失效， 可用以下命令来忽略：

```
$ git update-index --assume-unchanged filename

```

撤销可使用指令：

```
$ git update-index --no-assume-unchanged filename
```


## 后记

以上为收集整理的部分git指令，欢迎补充完善，如有遗误，还请指正。
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=0 height=0 src="http://music.163.com/outchain/player?type=2&id=28978321&auto=0&height=0"></iframe>
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="https://music.163.com/outchain/player?type=2&id=28978321&auto=1&height=66"></iframe>

<!-- >最近访客

<div class="ds-recent-visitors" data-num-items="28" data-avatar-size="42" id="ds-recent-visitors"></div>
<br> -->