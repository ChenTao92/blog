---
title: 有些“奇怪”的scroll事件监听
date: 2019-06-10 15:20:35
categories: JS
tags: 
  - scroll
  - 事件冒泡
  - 事件捕获
---
## 前言  
> 在前端开发的过程中，对事件做监听是最平常不过了，最近在一个H5项目中监听scroll滚动事件的时候，发现居然有一个奇怪的问题，经过一番查找，总算找到原因，这里总结一下以供大家参考。  

## 问题简述
```html
<body>
    <div class="app">
        <div class="home">
        </div>
    </div>
</body>
```
```css  
.home {
    height: 100%;
    overflow-y: auto;
}
```
```javascript
window.addEventListener('scroll', this.scrollHandle);
```
在这样一个DOM结构中，我们在window对象上监听页面的滚动事件，但实际上滚动并未触发`scrollHandle`函数，也就是说没有监听到滚动事件，这里不禁让人有些奇怪了，然鹅，当我们添加第三个参数为true的时候，却又可以正常监听到滚动事件了，究竟是什么原因呢？这便是今天本文要讲述的问题了。<!-- more -->
```javascript
window.addEventListener('scroll', this.scrollHandle，true);
```
这里，我们先来了解一下事件流、事件捕获以及事件冒泡。  

## 事件流、事件捕获、事件冒泡  
事件流的起源：在浏览器发展到第四代的时候，浏览器开发团队遇到一个问题：页面的哪一部分会拥有某个特定的事件？要明白这个问题问的是什么，可以想象画在一张纸上的一组同心圆。如果你把手指放在圆心上，那么你的手指指向的不是一个圆，而是纸上的所有圆。也就是说如果单击了页面的某个按钮，同时也单击了按钮的容器元素，甚至单击了整个页面。不过呢，IE提出的是***冒泡流（bubble）***，而网景提出的是***捕获流（capture）***。  
![](http://ww1.sinaimg.cn/large/873fcdb7ly1g37lz1s9yhj20ay09x41y.jpg)  
- 后来在W3C组织的统一之下，JS支持了冒泡流和捕获流。
- 总结就是：先捕获，后冒泡，捕获从上到下，冒泡从下到上（形象点说法：捕获像石头沉入海底，冒泡则像气泡冒出水面）。
- ***默认情况下，事件监听器使用冒泡事件流，不使用捕获事件流***。然而，你可以显式的指定使用捕获事件流，方法是在注册事件时传入第三个参数`useCapture`参数，将这个参数设为`true`。  
![](http://ww1.sinaimg.cn/large/873fcdb7gy1g37mernj0nj21dc106do0.jpg)  

## 具体到scroll事件呢？  
前面我们提到了事件有捕获流（自上而下）和冒泡流（自下而上），但是我们进一步查阅资料发现，scroll事件有些特殊，在绝大多数浏览器上，[***scroll事件是不会发生事件冒泡（Event bubbling）***](https://www.quirksmode.org/dom/events/scroll.html) 的。  
![](http://ww1.sinaimg.cn/large/873fcdb7gy1g37mmkyxwyj220o122wl5.jpg)  
所以，这样出现前面现象，原因就好解释了。  
> scroll事件是触发在`<div class="home"></div>`上，由于scroll事件的不会发生事件冒泡（event bubbling），所以我们自然不会在window上面监听到scroll事件，而在注册事件时传入第三个参数`useCapture`，并将这个参数设为`true`时，会让事件监听器在捕获事件流工作，从而在最上方的window对象上就捕获和监听到事件。  

## 验证和改进  
对于前面提到的DOM结构，因为对应的css是：  
```css
.home {
    height: 100%;
    overflow-y: auto;
}
```
实际上scroll事件是发生在`<div class="home">`内，而非滚动了整个页面，所以对监听器修改成：  
```javascript
document.querySelector(".home").addEventListener('scroll', this.scrollHandle);
```
就可以使得监听器正常工作了，或者我们配置`capture参数`的值为`true`，指定监听器使用捕获事件流：  
```javascript
window.addEventListener('scroll', this.scrollHandle, true);
```


<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=0 height=0 src="http://music.163.com/outchain/player?type=2&id=28387594&auto=0&height=0"></iframe>
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="https://music.163.com/outchain/player?type=2&id=28387594&auto=1&height=66"></iframe>

<!-- >最近访客

<div class="ds-recent-visitors" data-num-items="28" data-avatar-size="42" id="ds-recent-visitors"></div>
<br> -->