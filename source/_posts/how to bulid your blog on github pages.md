---
title: 怎样使用Hexo通过Github Pages搭建个人博客
date: 2016-08-22 16:08:15
---
# 准备工作

## 注册[Github](http://www.github.com)

## 配置SSH keys和添加SSH Key到Github
- 生成密钥,`ssh-keygen`，一路回车
<!--more-->
```
Last login: Wed Jul 13 20:00:16 on ttys001
//生成密钥，输入后一路回车
TC-MacBookpro: ~ taochen$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/taochen/.ssh/id_rsa): 
Created directory '/Users/taochen/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /Users/taochen/.ssh/id_rsa.
Your public key has been saved in /Users/taochen/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:ckScuB/7NjJDT0OUbtm1dp4lUiPr5mcV3xJqVzcR9Ng taochen@TC-MacBookpro.local
The key's randomart image is:
+---[RSA 2048]----+
|       o.. .  .o.|
|      ..o o . ++.|
|       ..o o =.oE|
|      ... = + =o=|
|      ..S= . + *O|
|       o+ o = o.=|
|       . + = . o |
|        + = . o  |
|         = . o   |
+----[SHA256]-----+
TC-MacBookpro:~ taochen$ 
```

- 密钥生成以后，存放在`~/.ssh`目录中
```
//接下来要找到密钥的位置，并用cat指令打开公钥的内容，复制后粘贴到github中
TC-MacBookpro:~ taochen$ ls ~/.ssh
id_rsa      id_rsa.pub  
TC-MacBookpro:~ taochen$ cat ~/.ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDA9vWA+ToVHjvMV+9LerF7hdHawqh3ls8OVSsYNfP2ppLS4/w47cWKOR+9Dv6cgqJLstDt7mgnbcpFcztLYZKBy1ZPBgOA1tYtxDIA2pDMIKsYd9M7UosgavCMgBg0dlO1y+xUhnyShqduDHblCCUjM9HECSWZyBD+rB6HipeNQWZLWNZ39anPvjTp9ZizdDTtPYi7zaB/fcUatqxeHkFUjHm6WYaC/gQrYsqj1LAonQCLhXHNHL8VZg2sXTxl+AOZfX1ZGTs3qXFAoUkDWaMQKC3UNTNrUC9MwNcs/+cLecaDMf+5BBxI71xRP+JLvAy1mTeux2vk0d9FyLU7et9d taochen@TC-MacBookpro.local
TC-MacBookpro:~ taochen$ 
```
- 到github中添加密钥（把ssh的公钥给github）

![](http://ww1.sinaimg.cn/bmiddle/873fcdb7gw1f7js23dlmbj20qu0wejtv.jpg)
![](http://ww1.sinaimg.cn/large/873fcdb7jw1f7jspdbjqxj21ht0vlgv4.jpg)

- 添加远程仓库，注意要使用ssh而不是https协议

![](http://ww3.sinaimg.cn/large/873fcdb7jw1f7jsrf98esj21800o443s.jpg)
![](http://ww4.sinaimg.cn/large/873fcdb7jw1f7jstvfchqj20wm0qu0w9.jpg)
![](http://ww1.sinaimg.cn/large/873fcdb7jw1f7jsv3h567j21kw12rgx5.jpg)

- 密钥添加好并建好仓库后就可以推送代码了

# 使用Github Pages建立博客

## github上建立仓库

在github首页，点击页面右下角`New Repository`

仓库名：`username.github.io`
e.g. 
![](http://ww4.sinaimg.cn/large/873fcdb7jw1f7jt682c22j21ew0xe0y6.jpg)

***注：Github Pages的Repository的名字是有规定格式的，否则Github无法识别这个Repository是你的Github账户的Pages***

# 在本地创建名为`blog`的文件夹(文件夹名随意，此处以blog为例)，并初始化为git仓库

``` bash
$ mkdir blog

$ cd blog

$ git init

```

## Hexo模板引擎

### Hexo是什么

Hexo是一个简单、快速、强大的博客发布工具，支持Markdown格式，具体详见[官方介绍](https://hexo.io/docs/index.html)。

### 安装Hexo

`$ npm install -g hexo`

### 初始化Hexo

`$ hexo init`

Hexo随后会自动在目标文件夹里建立网站所需的所有文件。

## 安装Hexo主题

### 复制主题

可以去[Hexo官网](https://hexo.io/themes/)寻找你喜欢的主题，这里我们选用Apollo主题为例

` $ git clone https://github.com/pinggod/hexo-theme-apollo.git  apllo`

### 启用主题

修改`_config.yml`配置文件中的`theme`配置项为`apollo`



## 配置deploy

在`_config.yml`文件中配置deploy

```bash
type: github
repository: https://git@github.com:username/username.github.io.git
branch: master
```

## 遇到“Deployer not found: github”时的解决办法

`$ npm install hexo-deployer-git --save`



## 写博客

在 `blog/source/_posts/ `目录下建立`.md`文件，写入Markdown格式文稿即可。

## 推送到github仓库区可以先在本地查看效果

`$ hexo s`

然后到浏览器输入localhost:4000查看效果。



## 推送到Github

```bash
$ hexo g

$ hexo d

```



