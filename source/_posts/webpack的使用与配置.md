---
title: webpack的使用与配置
date: 2016-11-09 15:11:43
categories: front End
tags:
 - webpack
---
### 什么是Webpack
WebPack可以看做是模块打包器：它做的事情是，分析你的项目结构，找到JavaScript模块以及其它的一些浏览器不能直接运行的拓展语言（Scss，TypeScript等），并将其打包为合适的格式以供浏览器使用。
![](http://ww1.sinaimg.cn/bmiddle/9bd18299gw1f9f5147i48j21kw0sfaeh.jpg)
<!-- more -->
### 为什么要使用WebPack
现今的很多网页其实可以看做是功能丰富的应用，它们拥有着复杂的JavaScript代码和一大堆依赖包。为了简化开发的复杂度，前端社区涌现出了很多好的实践方法
模块化，让我们可以把复杂的程序细化为小的文件;
优化页面加载，webpack可以将原本需要额外消耗请求的依赖打包放在一个文件里
这些改进确实大大的提高了我们的开发效率，但是利用它们开发的文件往往需要进行额外的处理才能让浏览器识别,而手动处理又是非常反锁的，这就为WebPack类的工具的出现提供了需求。

### 准备工作
1.

![](http://ww4.sinaimg.cn/bmiddle/9bd18299gw1f9f5gee73oj205m02hmx4.jpg)
创建两个文件夹分别为`dist`和`src`，将源码(开发文件)放在`src`这个文件夹里，将生产文件放在`dist`文件夹里。

2.每个项目下都必须配置有一个`webpack.config.js`（可以取任意的名字，但是推荐用该配置名），告诉`webpack`它需要做什么。

3. 
```bash
$ npm init //生成package.json文件，存放依赖
```

4.在安装依赖的时候文件夹中还会多增加一个`node_modules`的文件夹，里面含有大量的文件，当我们在sublime中添加项目文件夹的时候为了避免因为显示`node_modules`文件夹而造成卡顿，可以在sublime的`preferences-settings-User`里面添加下面的配置。
```bash
"folder_exclude_patterns":
  [
      "node_modules",
  ]
```

5.添加`.gitignore`文件。
```bash
.DS_Store
node_modules
```

### 安装
首先安装`webpack`
```bash
$ npm install --save webpack //项目文件夹安装
```

### 配置
首先来配置webpack.config.js
```bash
module.exports = {
  entry:   "./src/main.js",//唯一入口文件
  output: {
    path: path.join(__dirname, "dist"),//打包后的文件存放的地方
    filename: "bundle.js"//打包后输出文件的文件名
  }
}
```
>注：“__dirname”是Node.js中的一个全局变量，它指向当前执行脚本所在的目录。

现在如果你需要打包文件只需要在终端里你运行webpack命令就可以了，这条命令会自动参考webpack.config.js文件中的配置选项打包你的项目。
接下来配置packjson.json
在安装那里，我们提到过`npm init`，一路回车就行了，再打开package.json里面可以看到下面的信息。
```bash
{
  "name": "",//项目名字，自动生成
  "version": "1.0.0",
  "description": "",
  "scripts": {
    //我们需要配置的地方就是这里啦
  },
  "author": "bai",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^1.12.9"//所需要用的依赖
  }
}
```


### 使用webpack构建本地服务器
想不想让你的浏览器监测你都代码的修改，并自动刷新修改后的结果，其实Webpack提供一个可选的本地开发服务器，这个本地服务器基于node.js构建，可以实现你想要的这些功能，不过它是一个单独的组件，在webpack中进行配置之前需要单独安装它作为项目依赖
```
$ npm install –save webapck-dev-server
```
然后在"script"处配置脚本语言
 `"dev": "webpack-dev-server --content-base dist --port 6200 --color --progress --hot --inline"`
想要运行这段脚本，需要这样用npm run dev 就可以构建一个服务器了。

### Loaders
通过使用不同的loader，webpack通过调用外部的脚本或工具可以对各种各样的格式的文件进行处理，比如说把ES6转换为现代浏览器可以识别的JS文件。或者使用React开发时，合适的Loaders可以把React的JSX文件转换为JS文件。
Loaders需要单独安装并且需要在webpack.config.js下的modules关键字下进行配置，Loaders的配置选项包括以下几方面：
- test：一个匹配loaders所处理的文件的拓展名的正则表达式（必须）
- loader：loader的名称（必须）
- include/exclude:手动添加必须处理的文件（文件夹）或屏蔽不需要处理的文件（文件夹）（可选）；
- query：为loaders提供额外的设置选项（可选）

### Babel 
首先安装babel需要的依赖
```bash
$ npm install --save-dev babel-core babel-loader babel-preset-es2015 babel-preset-react
```

在webpack中的配置如下
```javascript
module.exports = {
  entry: "./src/main.jsx",
  output: {
    path: path.join(__dirname, "dist"),
    filename: "bundle.js",
  },
  module: {
    loaders: [
      {
        test: /\.jsx$/,
        loader: "babel",
        exclude: /node_modules/,
      }
    ]
  }
}
```
Babel其实可以完全在webpack.config.js中进行配置，但是考虑到babel具有非常多的配置选项，在单一的webpack.config.js文件中进行配置往往使得这个文件显得太复杂，因此一些开发者支持把babel的配置选项放在一个单独的名为 ".babelrc" 的配置文件中。我们现在的babel的配置并不算复杂，不过之后我们会再加一些东西，因此现在我们就提取出相关部分，分两个配置文件进行配置（webpack会自动调用.babelrc里的babel配置选项），如下：

```
{
  "presets": ["react", "es2015"]
}
```
### CSS

webpack提供两个工具处理样式表`css-loader`和`style-loader`，二者处理的任务不同`css-loader`使你能够使用类似`@import`和`url(...)`的方法实现`require()`的功能,`style-loader`将所有的计算后的样式加入页面中，二者组合在一起使你能够把样式表嵌入webpack打包后的JS文件中。
```bash
$ npm install --save style-loader stylus stylus-loader css-loader
```
在webpack中的配置如下
```javascript
module.exports = {
  entry: "./src/main.jsx",
  output: {
    path: path.join(__dirname, "dist"),
    filename: "bundle.js",
  },
  module: {
    loaders: [
      {
        test: /\.jsx$/,
        loader: "babel",
        exclude: /node_modules/,
      },
       {
        test: /\.styl$/,
        exclude: /node_modules/,
        loader: "style!css!stylus",
      },
      {
        test: /\.css$/,
        loader: "style!css",
      },
    ]
  }
}
```
在前面提到过，webpack只有单一的入口，其它的模块需要通过`import`,`require`,`url`等导入相关位置，为了让webpack能找到`main.css`文件，我们把它导入`main.jsx`中，如下
```
//main.jsx
import React from 'react';
import {render} from 'react-dom';
import './scr/main.css';//使用require导入css文件
```

以上就是目前使用到的webpack的一些知识啦~后期还会继续完善的。