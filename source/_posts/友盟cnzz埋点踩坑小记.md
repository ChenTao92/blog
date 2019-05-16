---
title: 友盟cnzz埋点踩坑小记
date: 2019-05-16 15:08:39
categories: Web
tags: 
  - cnzz
  - 埋点
---
## 前言
> 在近期的一个`Vue+Webpack`前端项目中，遇到需要打开H5页面后，针对不同环境进行差异化访问埋点（如在微信中、在嵌入的客户端中埋不同的点）的需求。这里选择使用友盟cnzz来进行代码埋点和数据追踪统计，现在把遇到的坑和解决方案记录下来供大家参考。

### 基本配置  
友盟cnzz封装`umeng.js`
```javascript
var umId = 1234567890;//这里填你的siteId
var umScript = document.createElement('script');
umScript.src = 'https://s95.cnzz.com/z_stat.php?id=' + umId + '&web_id=' + umId;
umScript.setAttribute('language', 'JavaScript');
document.head.appendChild(umScript);

var _czc = [];
umScript.onload = function () {
  //声明_czc对象
  var _czc = _czc || [];
  //绑定siteid
  _czc.push(["_setAccount", umId]);
  /* ... */
};
```
<!-- more -->
在入口文件`main.js`中引入  
```javascript
import "./umeng";
```
具体业务逻辑`xxx.vue`文件中  
```javascript
<script>
    export default {
        mounted() {
            if (/* ... */) {
                _czc.push(["_trackEvent", 'xxx', 'yyy']);
            }
        }
    }
</script>
```
这里我们埋的是事件点，好了，埋点完成，跑一下代码，结果发现错误提示：  
```
VM13155:1 Uncaught ReferenceError: _czc is not defined
```
问题来了，这里为什么_czc对象会找不到呢？
> 原因是，_czc对象的声明和绑定siteId是在onload事件中执行的，此时mounted钩子触发后，可能还未触发onload事件，所以找不到这个对象很正常。所以，我们需要在onload事件完成后，才去执行埋点操作。但是这种操作判断，不适合写到具体业务逻辑中，故我们将其逻辑处理同样放到`umeng.js`中，大致思路是，export出一个新的埋点方法`logEvent`，声明两个新的变量，用`loaded`标记onload事件的触发状态，用`events`本地缓存尚未onload时的埋点请求，待onload后统一执行埋点。

### 优化如下
友盟cnzz封装`umeng.js`
```javascript
var umId = 1234567890;//这里填你的siteId
var umScript = document.createElement('script');
umScript.src = 'https://s95.cnzz.com/z_stat.php?id=' + umId + '&web_id=' + umId;
umScript.setAttribute('language', 'JavaScript');
document.head.appendChild(umScript);

var _czc = [];
let loaded = false;//用于标记onload的触发状态
let events = [];//用于本地缓存尚未onload时的埋点请求
umScript.onload = function () {
  //声明_czc对象
  var _czc = _czc || [];
  //绑定siteid
  _czc.push(["_setAccount", umId]);
  loaded = true;
  events.forEach(e => {
    _czc.push(e)
  });
  /* ... */
};

export const logEvent = (category, action) => {
  if(loaded) _czc.push(["_trackEvent", category, action]);
  else events.push(["_trackEvent", category, action])
}
```
具体业务逻辑`xxx.vue`文件中  
```javascript
<script>
    import {logEvent} from "./umeng";
    export default {
        mounted() {
            if (/* ... */) {
                logEvent('xxx', 'yyy');
            }
        }
    }
</script>
```
好了，这下没有报错问题了，然而经过多次测试，友盟cnzz统计后台结果显示，并没有统计到所埋的事件点。经过多方排查，找到原因如下：  
> 友盟cnzz埋点的原理，大致是通过注入一段script脚本，然后这段脚本会给window对象下的_czc提供一个特别的push方法，这样我们通过调用_czc的push方法，把参数信息传递到友盟cnzz的后台。

这里简要介绍下ES6的模块化
> 在ES6中模块作为重要的组成部分被添加进来。模块的功能主要由 export 和 import 组成。每一个模块都有自己单独的作用域，模块之间的相互调用关系是通过 export 来规定模块对外暴露的接口，通过import来引用其它模块提供的接口。同时还为模块创造了命名空间，防止函数的命名冲突。

前面我们所封装的`umeng.js`，是通过`ES6的模块化export导出和import引入`到业务代码中的。所以，我们在`umeng.js`中声明的`_czc`空数组，并不会被友盟cnzz的脚本初始化，被初始化的是window对象下的`_czc`，所以在`umeng.js`中的`_czc`的push方法只是一个普通数组拥有的push方法，往这里的`_czc`里面push埋点参数，并不会触发任何cnzz的逻辑。

### 再次优化
修改`umeng.js`如下：
```javascript
var umId = 1234567890;//这里填你的siteId
var umScript = document.createElement('script');
umScript.src = 'https://s95.cnzz.com/z_stat.php?id=' + umId + '&web_id=' + umId;
umScript.setAttribute('language', 'JavaScript');
document.head.appendChild(umScript);

let loaded = false;//用于标记onload的触发状态
let events = [];//用于本地缓存尚未onload时的埋点请求
umScript.onload = function () {
  //声明_czc对象
  window._czc = window._czc || [];
  //绑定siteid
  _czc.push(["_setAccount", umId]);
  loaded = true;
  events.forEach(e => {
    _czc.push(e)
  });
  /* ... */
};

export const logEvent = (category, action) => {
  if(loaded) _czc.push(["_trackEvent", category, action]);
  else events.push(["_trackEvent", category, action])
}
```
至此，我们终于成功的将事件点成功埋上，在友盟cnzz收到了统计数据。

## 总结
- 未onload的时候，要把埋点缓存下来，待onload后统一执行埋点
- 声明和初始化的`_czc`，一定要声明在window对象下，不要写在函数里，且只需要定义一次

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=0 height=0 src="http://music.163.com/outchain/player?type=2&id=28387594&auto=0&height=0"></iframe>
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="https://music.163.com/outchain/player?type=2&id=28387594&auto=1&height=66"></iframe>

<!-- >最近访客

<div class="ds-recent-visitors" data-num-items="28" data-avatar-size="42" id="ds-recent-visitors"></div>
<br> -->