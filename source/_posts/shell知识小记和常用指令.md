---
title: shell知识小记和常用指令
date: 2016-09-01 09:12:42
categories: Note
tags: 
  - shell
  - 流与重定向
  - 管道
---
前些日子，整理了一些Shell相关的笔记，在此记录下来备用

## shell

- shell是一个程序，中文名叫`命令解释器`
- 所有测试基于`bash`，shell里面，每个指令有一个返回值，0表示成功，非0表示不成功。上一个指令的返回值可以通过`$?`查看
<!--more-->
- `cd`是shell内建的命令，不是独立的程序
- 操作系统会自带一些常见指令：如`ls` `cal` `date`等


## shell的变量

shell的变量可以直接使用`name=val`这种形式来赋值***（注意中间没有空格，不能有空格）***，查看变量的时候，在变量名前面加上`$`符。

```bash
$ name=Jerry
$ echo $name
Jerry
```

注意：解释`name`变量的是shell，而不是`echo`指令，shell解释完毕后把结果交给`echo`，由`echo`打印出来


## PATH 变量

- `PATH` 变量是shell的一个环境变量，通常情况下，环境变量应该大写。可用在创建它们的shell和从该shell派生的任意子shell或进程中。它们通常被称为全局变量，以区别于局部变量。

- `PATH`是由冒号`：`分割的，可通过`echo $PATH`查看：

```
$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
```

shell在执行一个程序时，会先去`PATH`指定的目录中找对应的程序，如果找到了则执行，没找到则会提示`command not found`。所以，如果把`PATH`变量赋值为空的话，在shell中我们将无法执行任何二进制程序。


## 文件描述符，流与重定向

每一个程序会默认打开三个流：
- 标准输入（stdin），标准输出（stdout），标准错误输出（stderr）。

shell使用`文件描述符`来标识一个流，分别为：
- 标准输入： 0
- 标准输出： 1
- 标准错误输出： 2

`a>b`，文件描述符a重新定向到b指向的地方。一般是重定向到文件或者设备。
*注意：b不是指代文件描述符*
```bash
$ ls 1>a.txt
```
此时我们在终端里将看不见任何内容，因为ls的输出已经被放到a.txt这个文件里面去了.
由于重定向标准输入太常见了，shell允许简写，所以上述代码可直接写成:
```bash
$ ls > a.txt
```
再来看重定向到设备，
```bash
$ ls > /dev/null
```
执行后在终端同样看不到任何结果，因为标准输出被重定向到`/dev/null`这个设备，而`/dev/null`是一个“黑洞”设备，数据会被丢弃。[*相关阅读*](http://blog.csdn.net/kaiwii/article/details/7308729)
```bash
$ ls -l > a.txt 2>a.txt
$ ls -l > a.txt 2>&1
```
第一行代码，表示把`ls -l`的标准输出定向到`a.txt`，再把标准错误输出（用文件描述符`2`表示）定向到`a.txt`。
也可以用上述第二行的写法，把某一个描述符定向到另一个描述符的指向，`2>&1`表示把标准错误输出定向到标准输入的指向，实质上等同于第一行代码。

另外，重定向除了是`>`，还可以是`>>`，表示追加:
```
$ echo abc >a.txt
$ cat a.txt
abc
//将abc写入a.txt

$ echo 123 >a.txt
$ cat a.txt
123
//123写入a.txt,原先的abc会被覆盖，不再保留

$ echo ABC >>a.txt
$ cat a.txt
123
ABC
//新写入的ABC和原先a.txt中的123都在，>>表示追加
```

## 管道

管道操作符为`|`，可以将一个指令的标准输出连接到另一个指令的标准输入。

```bash
$ ls | grep A # 将当前目录下所有含有A的文件名列出来
```

首先`ls`会输出当前目录下的所有文件，然后标准输出会连接到`grep`的标准输入，最后`grep`执行将筛选出当前目录所有含有A的文件名。

## 命令替换

命令替换和重定向有些相似，但区别在于命令替换是将一个命令的输出作为另外一个命令的参数。常用命令格式为：
```bash
$ command1 `command2` 
```

其中，`command2`的输出将作为`command1`的参数。需要注意的是这里的\`符号，被它括起来的内容将作为命令执行，执行后的结果作为`command1`的参数。例如：
```
$ cd `pwd`
```
该命令将pwd命令列出的目录作为cd命令的参数，结果仍然是停留在当前目录下
```
$ echo `echo 123`
123
```
反引号里的echo会先打印字符串123到标准输出，这个输出将作为外面的echo的参数，再执行后输出字符串123

## 别名

别名就是可以给一个指令创建另外一个名字，一般是用来简化指令的，少打几个字符。

```bash
$ alias tc=ls # 输入tc相当于输入ls
$ alias # 直接输入alias，可以看到当前shell中所有的别名
```


## shell通配符

`{a..z}`会自动扩展。

```
$ echo A{a..z}
Aa Ab Ac Ad Ae Af Ag Ah Ai Aj Ak Al Am An Ao Ap Aq Ar As At Au Av Aw Ax Ay Az
```

`*`号shell会替你自动替换。

```
$ ls
a.jpg  b.jpg  c.txt  d.html
//假设当前文件夹包含文件如上

$ ls *.jpg
a.jpg b.jpg
//shell会先查找符合*.jpg的文件，找到后把参数*.jpg替换成a.jpg和b.jpg再传给ls执行

$ rm *.jpg
$ ls
c.txt  d.html
//同样shell会先把*.jpg替换成a.jpg和b.jpg，再传给rm执行删除

$ ls *.jpg
ls: *.jpg: No such file or directory
shell没有找到符合*.jpg的文件，直接把参数*.jpg给ls
```


## 子shell，环境变量

- `环境变量`是当前shell中能够被子shell继承的变量，普通变量不能被继承。在子shell里修改变量，是不会影响父shell的。在父shell中存在的变量，会在子shell继承。
- 使用指令`export name=val`可将普通变量导出为环境变量。
- 用圆括号括起来的指令会在`子shell`中执行，不会对`父shell`造成影响：`(ls)`
```
$ pwd
/Users/tc/Documents
$ (cd ..;ls)
Applications     Documents      Movies
Public           Downloads      Music
Desktop          Library        Pictures
$ pwd
/Users/tc/Documents
```
上述代码我们可以看到，`父shell`目录在`/Users/tc/Documents`，在`子shell`中执行`cd ..;ls`后，对`父shell`所在目录不会造成影响，仍然在`/Users/tc/Documents`，并不会返回上一级目录
- 变量赋值还可以使用这种语法：`name=$(command)`，`子shell`前面加上`$`，会得到`command命令`的输出做为`变量name`的值。
如：`name=$(ls)`，会把`ls`在`子shell`中的执行结果给`变量name`。
```
$ name=$(ls)
$ echo $name
Applications Desktop Documents Downloads Library Movies Music Pictures Public
```

## shell的配置文件

一般为`~/.bashrc`、`~/.zshrc`
配置文件比较正规的叫法是：运行控制文件(run control)
打开shell时，会先加载读取`~/.zshrc`配置文件

## 在shell中不换行执行多个指令

- 使用分号`;`
- 使用`&&`或者`||`号。

## 常用指令
- `ls`：list，表示显示当前目录下所有的内容。
注意：`ls`根本不从`标准输入`里面读取内容，其参数是通过命令行直接提供给它的。
- `cd`：change directory, 改变你当前的工作目录
- `rm`：remove，删除文件或者文件夹，使用的时候需要小心，删除的东西不会到回收站中，会直接删掉
- `mkdir`：make directory，建立文件夹
- `rmdir`：remove directory，删除文件夹，只能删除空的文件夹，非空的文件夹需要使用`rm`并配合`-r`参数来删除，也就是`rm -r [dir name]`
- `cp`：copy，拷贝文件，比如`cp a.txt b.txt`，将`a.txt`文件拷贝一份，名字叫做`b.txt`
- `touch`：新建一个空的文件，比如`touch a.txt`，在当前目录下新建一个名叫`a.txt`的空文件
- `pwd`：print working directory，打印当前的工作目录，也就是你当前在什么目录当中
- `cat`：查看文本文件的内容
- `open`：用来将路径在finder中打开
- `echo`：打印字符串到标准输出
- `mv`：移动或重命名文件
- `ping`：通过检查网络中其他主机的应答信息，来确认网络的连通性，语法：ping[参数]主机名（或ip地址）
- `grep`：在文件中搜寻匹配的行并进行输出，语法：grep[参数]<要找的字串><原文件>
- `exit`：退出当前shell
- `export`：设置与显示环境变量
- `which`：显示指令的绝对路径，可以用来看一个指令的类型， e.g.
```
$ which pwd
pwd: shell built-in command
$ which cd
cd: shell built-in command
$ which cal
/usr/bin/cal
```
说明：`pwd`、`cd`是shell内建的命令，不是程序，而`cal`是`/usr/bin/`目录下的一个二进制程序

## 其他指令

- `who`：显示当前已登录用户的信息
- `whoami`：显示当前用户名
- `awk`：可以拿来做命令行级别的编辑，比如想挑出数据的第8列：
```
$ ls -l
total 0
drwx------   6 tc  staff   204  8 21 01:05 Applications
drwxr-xr-x@  6 tc  staff   204  8 22 20:17 Applications (Parallels)
drwxr-xr-x  22 tc  staff   748  8 25 15:24 Desktop
drwx------+ 14 tc  staff   476  8 24 22:42 Documents
drwx------+ 20 tc  staff   680  8 25 04:18 Downloads
drwx------@ 56 tc  staff  1904  8 16 21:24 Library
drwx------+  7 tc  staff   238  7 26 17:47 Movies
drwx------+ 11 tc  staff   374  6 25 17:56 Music
drwx------+  6 tc  staff   204  8  4 02:08 Pictures
drwxr-xr-x+  6 tc  staff   204  4  7 22:39 Public
$ ls -l | awk '{print$8}'

01:05
20:17
15:24
22:42
04:18
21:24
17:47
17:56
02:08
22:39
```
注意：`awk`后面的参数要跟单引号，因为没有引号或者双引号，会被shell先扩展变量做出解释后再被`awk`拿到，而跟单引号转义后会被`awk`直接拿到。
- `wc`：word count，用来统计单词数，用`-l`：line参数可以来统计行数:
```
$ ls -l
total 0
drwx------   6 tc  staff   204  8 21 01:05 Applications
drwxr-xr-x@  6 tc  staff   204  8 22 20:17 Applications (Parallels)
drwxr-xr-x  22 tc  staff   748  8 25 15:24 Desktop
drwx------+ 14 tc  staff   476  8 24 22:42 Documents
drwx------+ 20 tc  staff   680  8 25 04:18 Downloads
drwx------@ 56 tc  staff  1904  8 16 21:24 Library
drwx------+  7 tc  staff   238  7 26 17:47 Movies
drwx------+ 11 tc  staff   374  6 25 17:56 Music
drwx------+  6 tc  staff   204  8  4 02:08 Pictures
drwxr-xr-x+  6 tc  staff   204  4  7 22:39 Public
$ ls -l | wc -l
  11
```

从`total 0`开始一共是11行，说明一共有10个文件

## subl命令：后跟路径，可将此目录添加到sublime并启动

Sublime Text 编辑器自带一个`subl`程序，但第一次是无法运行的，因为其存在的默认路径不在`$PATH`中，建议修改如下：
```
$ cp /Applications/Sublime\ Text.app/Contents/SharedSupport/bin/subl  /usr/local/bin
```
e.g.
![](http://ww4.sinaimg.cn/large/873fcdb7gw1f85v11ggyhj21kw09bjv2.jpg)
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="http://music.163.com/outchain/player?type=2&id=35470197&auto=1&height=66"></iframe>

>最近访客

<div class="ds-recent-visitors" data-num-items="28" data-avatar-size="42" id="ds-recent-visitors"></div>
<br>