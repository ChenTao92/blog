---
title: 浅谈JavaScript异步机制与事件队列
date: 2016-09-12 06:24:01
categories: JavaScript
tags: 
  - 异步机制
  - 事件队列
  - setTimeout
---
`setTimeout`这样一个函数大家一定不会陌生，但是对于`setTimeout`这样看起来很简单的东西，却有一个细节相对比较容易忽视。我们来看一下，对`setTimeout`的描述通常像这样：给定一个回调及n毫秒的延迟，`setTimeout`就会在n毫秒后运行该回调。但实际上，这个描述是存在严重缺陷的，只能说在多数情况下，该描述只能算接近正确，但在其他某些情况下，则会出现严重的错误。
<!--more-->
首先，我们来看一个简单的例子，该例子常常会迷惑JavaScript新手：

```javascript
function f1() {
  console.log('1')
}
setTimeout(f1,0)
console.log("2")
```

大家认为运行结果的打印顺序会是先1再2，还是先2再1呢？
按照定式思维，`setTimeout`此时定义了`0毫秒延迟`后执行`f1`函数，所以应该是立即执行，打印出1，然后再执行下一条语句打印出2。

但实验之后，我们会发现，结果是先2再1。那在这个过程中，`setTimeout`究竟都干了些什么呢？
要想真正理解`setTimeout`，我们必须先大致了解JavaScript事件模型。

所有的JavaScript代码都由JavaScript脚本运行时引擎（Runtime），也叫`JavaScript解释器`来负责解释。在`JavaScript解释器`调用`setTimeout`的时候，浏览器的`timer模块`会进行延时处理，当时间到达的时候（如本例中为0毫秒后），就会产生一个事件排入`事件队列`（事件队列由浏览器负责维护），`setTimeout`会立刻返回。`setTimeout`做的事情就是产生一个事件，然后被放到`事件队列`里面，此时`f1函数`根本不会被执行。`JavaScript解释器`会直接执行下一行代码，直到出现空闲没有任何代码了，这时浏览器才会检查事件队列。如果队列中有事件，则浏览器会挑选出排在最前面的事件，并把此事件的处理器移到`JavaScript解释器`去执行。事件处理器返回后，我们又回到队列处，继续取出下一个事件的处理器到`JavaScript解释器`去执行。

所以，`console.log("2")`在没有被执行之前，`f1函数`不可能被执行，即使`setTimeout`中的延时为0毫秒。

如果JavaScript解释器一直不空闲，那事件队列中的事件就永远不会被触发，就像下面例子这样：
```javascript
function f1() {
  console.log('1')
}

setTimeout(f1,0)
console.log("2")

while(true) {
  
}
```

由于出现了一个while无限循环，造成了线程的阻塞，躺在事件队列里面的`f1`将永远无法执行。所以输出结果中只会看见2。

```javascript
for (var i = 1; i < 4; i++) {
  setTimeout(function(){
    console.log(i)
  },0)
}

4
4
4
```

这样一个例子为什么会出现这样的输出结果而不是`1 2 3`，现在就不难理解了吧。同样的缘故，在for循环没有结束之前，事件队列中的function函数是不会被执行的。

所以说，`setTimeout`和`setInterval`一样，它们的异步机制决定了其计时精准度严格来说是不准确的。
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="https://music.163.com/outchain/player?type=2&id=426852063&auto=1&height=66"></iframe>

>最近访客

<div class="ds-recent-visitors" data-num-items="28" data-avatar-size="42" id="ds-recent-visitors"></div>
<br>