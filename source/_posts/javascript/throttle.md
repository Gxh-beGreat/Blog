---
title: 函数节流 - throttle
date: 2017-06-07 22:09:27
categories:
- JavaScript
tags:
- JavaScript
---
-----

> 函数节流 - throttle

-----

### 使用场景
在项目开发中，经常会遇到某一个函数会因 _事件回调_ or _用户操作_ 而频繁被调起的情况，例如 `onscroll`、`ontouchmove` 等，高频率的函数触发会导致性能的下降甚至让浏览器直接崩溃。so，怎么解决？函数节流就是其中的一个方法。

### 原理
函数节流的原理挺简单的，估计大家都想到了，那就是定时器。当触发一个函数时，先setTimout让这个函数延迟一会再执行，如果在这个时间间隔内又触发了该函数，那我们就clear掉原来的定时器，重新定义一个新的定时器，这样便能控制函数不被高频触发了。

### 代码实现
```javascript
function throttle(fn, context, delay, value){
  clearTimeout(fn.timeoutId)
  fn.timeoutId = setTimeout(function(){
    fn.call(context, value)
  },delay)
}
```
`throttle`函数将定时器id作为自身的一个属性，当 `delay` 时间段内被再次被调用的时候，将会 `clear` 掉上一个定制器，并重新定义新的定时器。
