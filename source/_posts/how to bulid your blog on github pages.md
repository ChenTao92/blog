---
title: 怎样使用Hexo通过Github Pages搭建个人博客
date: 2016-08-22 16:08:15
---
# 前言
在搭建博客的过程中参考了一些其他作者的资料，也遇到了一些小问题，所幸在Google之后找到了解决方案，感谢这些作者们，于是我决定把搭建的全过程记录下来，希望能够给大家提供一些参考。

# 准备工作

## 注册Github账号
访问：http://www.github.com/

## 配置SSH keys和添加SSH Key到Github
#### 1. 终端中输入下列指令，生成密钥，一路回车
>$ ssh-keygen

<!--more-->
出现下面所示则表示密钥成功生成
```
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

#### 2. 密钥生成以后，存放在`~/.ssh`目录中
> $ ls ~/.ssh

```
$ ls ~/.ssh
id_rsa      id_rsa.pub
```

> $ cat ~/.ssh/id_rsa.pub
 

```
$ cat ~/.ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDA9vWA+ToVHjvMV+9LerF7hdHawqh3ls8OVSsYNfP2ppLS4/w47cWKOR+9Dv6cgqJLstDt7mgnbcpFcztLYZKBy1ZPBgOA1tYtxDIA2pDMIKsYd9M7UosgavCMgBg0dlO1y+xUhnyShqduDHblCCUjM9D+rB6HipeNQWZLWNZ39anPvjTp9ZizdDTtPYi7zaB/fcUatqxeHkFUjHm6WYaC/gQrYsqj1LAonQCdfdgHL8VZg2sXTxl+AOZfX1ZGTs3qXFAoUkDWaMQKC3UNTNrUC9MwNcs/+cLecaDMf+5BBxI71xRP+JLvAy1mTeux2vk0d9FyLU7et9d taochen@TC-MacBookpro.local 
```
复制出密钥内容
#### 3. 到github中添加密钥（把ssh的公钥给github）
登陆github系统。点击右上角的 Account Settings--->SSH and GPG keys --->New SSH key --->填写`Title`和`Key`
![](http://ww1.sinaimg.cn/bmiddle/873fcdb7gw1f7js23dlmbj20qu0wejtv.jpg)
![](http://ww1.sinaimg.cn/large/873fcdb7jw1f7jspdbjqxj21ht0vlgv4.jpg)

#### 4. 最后，点击`Add SSH key`确认添加密钥

# 使用Github Pages建立博客

## github上建立仓库

1. 在github首页，点击页面中的`Start a project`
![](http://ww3.sinaimg.cn/large/873fcdb7jw1f7jsrf98esj21800o443s.jpg)

2. 输入仓库名：`username.github.io` (username指你的github用户名，这里以`ChenTao92`为例)
![](http://ww4.sinaimg.cn/large/873fcdb7jw1f7jt682c22j21ew0xe0y6.jpg)

***注：Github Pages的Repository的名字是有规定格式的，否则Github无法识别这个Repository是你的Github账户的Pages***

## 在本地创建名为`blog`的文件夹，并初始化为git仓库
(文件夹名可随意，此处以`blog`为例)
``` bash
$ mkdir blog

$ cd blog

$ git init

```
这里用于存放建立blog的原始文件

## Hexo模板引擎

#### 1. Hexo是什么

Hexo是一个简单、快速、强大的博客发布工具，支持Markdown格式，具体详见[官方介绍](https://hexo.io/docs/index.html)。

#### 2. 安装Hexo

>$ npm install -g hexo

#### 3. 初始化Hexo

>$ hexo init

Hexo引擎随后会自动在目标文件夹里生成建立blog所需的所有文件。

## 更换Hexo主题

#### 1. 复制主题

可以去[Hexo官网](https://hexo.io/themes/)寻找你喜欢的主题，这里我们以选用Apollo主题为例:
- 先进入blog下面的themes文件夹
``` bash
➜  blog git:(master) ✗ cd themes
➜  themes git:(master) ✗ 

```
- 将主题的仓库代码克隆到本地
` $ git clone https://github.com/pinggod/hexo-theme-apollo.git  apllo`

#### 2. 启用主题

- 在编辑器中打开blog文件夹
- 修改`_config.yml`配置文件中的`theme`配置项为`apollo`
- `_config.yml`配置文件中的`title`和`author`等信息可根据自己的需要进行更改

## 配置deploy

在`_config.yml`文件中配置deploy（`username`以`ChenTao92`为例,请自行替换为自己的`username`）

``` bash
type: git
repository: https://git@github.com:ChenTao92/ChenTao92.github.io.git
branch: master
```

## 安装theme插件

`$ npm install --save hexo-renderer-jade hexo-generator-feed hexo-generator-sitemap hexo-browsersync hexo-generator-archive`

## 如遇到“Deployer not found: github”时的解决办法

`$ npm install hexo-deployer-git --save`


## 写博客

输入`hexo new "postName"`指令即可，hexo会在 `blog/source/_posts/ `目录下建立新的`.md`文件，写入Markdown格式博文即可。

## 启动本地服务，进行文章预览调试

> `$ hexo s`

- 然后到浏览器输入`http://localhost:4000/`查看效果。

- 查看效果完成后，可按`Ctrl+C`结束进程

## 推送到Github

```bash
$ hexo g

$ hexo d

```

## 访问博客

在以上步骤都成功完整后，浏览器访问链接 https://{username}.github.io 即可。
e.g. 
https://chentao92.github.io

