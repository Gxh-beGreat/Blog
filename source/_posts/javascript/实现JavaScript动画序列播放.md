---
title: 实现JavaScript动画序列播放
date: 2017-06-22 17:50:32
toc: true
mathjax: true
categories:
- JavaScript
tags:
- JavaScript
- 动画
---

-------

> [《用65行代码实现JavaScript动画序列播放》](https://www.h5jun.com/post/sixty-lines-of-code-animation.html) 读后总结

-------


### performance.now() 获取当前时间
在新的 [requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) 规范中，`frame` 回调的参数 `timestamp` 是一个 [DOMHighResTimeStamp](https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame) 对象，它比 `Date` 的计时要更精确（可以精确到纳秒）。因此获取时间我们优先使用 [performance.now()](https://developer.mozilla.org/en-US/docs/Web/API/Performance/now)，如果浏览器不支持 `performance.now()`，我们再降级使用 `Date.now()`。
```javascript
function nowtime(){
  if(typeof performance !== 'undefined' && performance.now){
    return performance.now()
  }
  return Date.now ? Date.now() : (new Date()).getTime()
}
```

### requestAnimationFrame polyfill
```javascript
if(typeof window.requestAnimationFrame === 'undefined'){
  window.requestAnimationFrame = function(callback){
    return setTimeout(function(){ //polyfill
      this::callback(nowtime())
    }, 1000/60)
  }
  window.cancelAnimationFrame = function(qId){
    return clearTimeout(qId)
  }
}
```

### Animator ES6实现
```javascript
export default class Animator {
  constructor(duration, update, easing) {
    this.duration = duration
    this.update = update
    this.easing = easing
  }

  animate = () => {
    let startTime = 0
    const { duration, update, easing } = this

    return new Promise((resolve, reject) => {
      let qId = 0

      function step(timestamp){
        startTime = startTime || timestamp
        var p = Math.min(1.0, (timestamp - startTime) / duration)

        self::update(easing ? easing(p) : p, p)

        if(p < 1.0){
          qId = requestAnimationFrame(step)
        }else{
          resolve(self)
        }
      }

      this.cancel = function(){
        cancelAnimationFrame(qId)
        self::update(0, 0)
        reject('User canceled!')
      }

      qId = requestAnimationFrame(step)
    })
  }

  // 能够传入新的 easing，并返回新的 Animator 对象，这样我们就可以在原动画的基础上扩展我们的动画效果
  ease = easing => {
    return new Animator(this.duration, this.update, easing)
  }
}
```
