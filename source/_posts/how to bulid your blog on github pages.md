---
title: 怎样使用Hexo通过Github Pages搭建个人博客
date: 2016-08-22 16:08:15
---
# 准备工作

- 注册[Github](http://www.github.com)

- 配置SSH keys和添加SSH Key到Github



# 使用Github Pages建立博客



## 在本地创建名为`blog`的文件夹，并初始化为git仓库

``` bash
$ mkdir blog

$ cd blog

$ git init

```



## github上建立仓库

在github首页，点击页面右下角`New Repository`

仓库名：`username.github.io`

***注：Github Pages的Repository的名字是有规定格式的，否则Github无法识别这个Repository是你的Github账户的Pages***



## Hexo框架

### Hexo是什么

Hexo是一个简单、快速、强大的博客发布工具，支持Markdown格式，具体详见[官方介绍](https://hexo.io/docs/index.html)。

### 安装Hexo

`$ npm install hexo`


### 部署Hexo

`$ hexo init`


Hexo随后会自动在目标文件夹里建立网站所需的所有文件。

此时已经搭建起本地的Hexo博客了，执行以下命令：


``` bash

hexo g

hexo s

```

然后到浏览器输入`localhost:4000`查看效果。



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

## 推送到github仓库区可以先在本地查看状态

`$ hexo s`

然后到浏览器输入localhost:4000查看效果。



## 推送到Github

```bash
$ hexo g

$ hexo d

```



