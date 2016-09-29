---
title: 如何用纯CSS实现带小三角形的会话框
date: 2016-08-12 11:07:31
categories: Web
tags: 
  - css
---
## 前言
生活中能看到很多带小三角形的会话框，如QQ、微信、时间轴等等，就像这样的：
![](http://ww1.sinaimg.cn/large/873fcdb7gw1f7pm570m9xj209803m74c.jpg)
在内容外面都有一个带小三角形的矩形框包围着，看上去视觉效果还不错，于是决定动手写一个，用纯CSS就能实现，下面我们来看一下。

## 基本原理

### 首先我们来看一下纯CSS实现三角形的原理：

其实就是对于border和transparent的应用
<!--more-->
```html
<body>
  <p>向上的三角:</p>
  <div class="top">
    <!--向上的三角-->
  </div>

  <p>向下的三角:</p>
  <div class="bottom">
    <!--向下的三角-->
  </div>

  <p>向左的三角:</p>
  <div class="left">
    <!--向左的三角-->
  </div>

  <p>向右的三角:</p>
  <div class="right">
    <!--向右的三角-->
  </div>
</body>
```

### 再用CSS分别实现向上、下、左、右的三角形

```css
/*向上的三角*/
.top {
  width: 0; 
  height:0; 
  border-left:50px solid transparent;
  border-right:50px solid transparent;
  border-bottom:50px solid red;
}

 /*向下的三角*/
.bottom {
  width:0; 
  height:0; 
  border-left:50px solid transparent;
  border-right:50px solid transparent;
  border-top:50px solid #0066cc;
}

    
 /*向左的三角*/
.left {
  width:0; 
  height:0; 
  border-top:50px solid transparent;
  border-bottom:50px solid transparent; 
  border-right:50px solid yellow; 
}
   
/*向右的三角*/
.right {
  width:0; 
  height:0; 
  border-top:50px solid transparent;
  border-bottom: 50px solid transparent;
  border-left: 50px solid green;
}
```

我们将上述代码整理在一起后，来看一下效果：
![](http://ww1.sinaimg.cn/large/873fcdb7gw1f7pi5gdhd7j206s0rsq3o.jpg)

很简单吧，好了，原理我们已经了解，接下来我们来实现带小三角形的会话框。

## 实现带小三角形的会话框

首先，写出html代码：
```html
<div class="dialog">
    <div class="trangle"><!--本Div只用来绘制三角形--> 
    </div>
    hello，world<br>
    hello，world<br>
    hello，world<br>
    hello，world<br>
  </div>
```

- `.trangle`的div只用来实现小三角形，对这个div用css的border和transparent实现三角形。
- 再通过定位和会话框组合

看下面css代码：

```css
/*下面开始来画带三角形的会话框*/

/*先关掉margin和padding，避免带来干扰*/
* {
  margin: 0;
  padding: 0;
}

body{
  background: gray;
  font-size: 20px;
  font-family: Comic Sans MS;
}

.dialog {
  margin: 0 auto;
  margin-top: 15px;
  width: 300px;            
  background: #fff;
  padding: 10px;
  border-radius: 5px;
}

.trangle{
  position: absolute;
  margin-left: -20px;
  width: 0;
  height: 0;
  border-top: 10px solid transparent;
  border-bottom: 10px solid transparent;
  border-right: 10px solid #fff;
}
```

效果是这样的：
![](http://ww4.sinaimg.cn/large/873fcdb7gw1f7plr95yzfj20y00goaas.jpg)

好了，到这里我们就完工了，剩下的就是做一些美化的工作了。
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=0 height=0 src="http://music.163.com/outchain/player?type=2&id=28387594&auto=0&height=0"></iframe>
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="https://music.163.com/outchain/player?type=2&id=28387594&auto=1&height=66"></iframe>

>最近访客

<div class="ds-recent-visitors" data-num-items="28" data-avatar-size="42" id="ds-recent-visitors"></div>
<br>