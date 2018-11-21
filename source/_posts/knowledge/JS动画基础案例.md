---
title: JS动画基础案例
date: 2017-06-21 10:50:32
toc: true
mathjax: true
categories:
  - 前端知识
tags:
  - JavaScript
  - 动画
---

---

> [《关于动画，你需要知道的》](https://www.h5jun.com/post/animations-you-should-know.html?from=singlemessage&isappinstalled=0) 学习笔记，将以 ES6 的语法实现原文案例

---

### 动画的简易封装

为了实现更加复杂的动画，我们可以将动画进行简易的封装，要进行封装，我们先要抽象出动画相关的要素：

- 动画时长：\\(T = duration\\)
- 动画进程：\\( p = \frac{t}{T}\ \(p \in [0, 1])\\)
- 动画量子 easing：\\(e = f(p)\\)
- 动画方程：\\([x, y] = G(e) = G(f(p))\\)
- 动画生命周期：开始、进行中、结束。

_动画的简易封装_

```javascript
class Animator {
  /**
   * @param  {Number}   duration 动画持续时间
   * @param  {Function} progress 过程处理函数，回调参数为 动画量子easing 及 动画进度 p
   * @param  {Function} easing   easing处理函数
   */
  constructor(duration, progress, easing) {
    this.duration = duration;
    this.progress = progress;
    this.easing =
      easing ||
      function(p) {
        return p;
      };
    this.rAF = window.requestAnimationFrame; // rAF 需要做兼容性判断
  }

  start = finished => {
    // 记录开始时间
    let startTime = Date.now();
    const { duration, progress, easing, rAF } = this;

    // 启动定时器
    rAF(function step() {
      // 记录动画进度
      const p = (Date.now() - startTime) / duration;
      let next = true;

      // 根据进度及finished参数，选择不同的 progress 回调方式，回调参数为 动画量子easing 及 动画进度 p
      if (p < 1.0) {
        progress(easing(p), p);
      } else {
        if (typeof finished === 'function') {
          next = finished() === false;
        } else {
          next = finished === false;
        }

        if (!next) {
          progress(easing(1.0), 1.0);
        } else {
          startTime += duration;
          progress(easing(p), p);
        }
      }

      // 是否继续执行定时器
      if (next) rAF(step);
    });
  };
}
```

在上面的代码里，我们封装出一个简易的动画类 `Animator`, 这个类的构造器接收三个参数，分别是 `duration`, `process` 和 `easing`。它产生一个对象，包含一个 `start` 方法，这个方法用指定 `duration`、`process` 和 `easing` 执行动画。  
`start` 方法包含一个参数，这个参数是一个布尔类型或者回调函数，当动画结束的时候，如果这个参数是回调函数，将执行这个函数，它的返回值如果不是 `false` 那么结束动画，否则循环播放动画。

### 动画队列

在构造更复杂的动画的时候，为了更方便使用，避免回调嵌套，我们可以再实现一个动画队列类：

```javascript
class AnimationQueue {
  constructor(animators) {
    this.animators = animators || [];
  }

  append = (...args) => {
    this.animators.push(...args);
  };

  flush = () => {
    const { animators } = this;
    const self = this;
    if (animators.length) {
      function play() {
        const animator = animators.shift();

        if (animator instanceof Animator) {
          animator.start(() => {
            if (animators.length) {
              play();
            }
          });
        } else {
          self::animator();
          if (animators.length) {
            play();
          }
        }
      }
      play();
    }
  };
}
```

### 基础案例

我们尝试使用上面设计的动画类来构造连续播放的动画：

[让滑块先向右然后再向下运动](https://jsfiddle.net/Amu_xh/3uvpLj2z/2/)

```javascript
const a1 = new Animator(1000, p => {
  const tx = 100 * p;
  block.style.transform = `translateX(${tx}px)`;
});

const a2 = new Animator(1000, function(p) {
  const ty = 100 * p;
  block.style.transform = `translate(100px, ${ty}px)`;
});

block.addEventListener('click', () => {
  a1.start(() => {
    a2.start();
  });
});
```

[让滑块沿一个矩形边界运动](https://jsfiddle.net/Amu_xh/q8fycj2p/)

```javascript
const a1 = new Animator(1000, p => {
  const tx = 100 * p;
  block.style.transform = `translateX(${tx}px)`;
});

const a2 = new Animator(1000, p => {
  const ty = 100 * p;
  block.style.transform = `translate(100px, ${ty}px)`;
});

const a3 = new Animator(1000, p => {
  const tx = 100 * (1 - p);
  block.style.transform = `translate(${tx}px, 100px)`;
});

const a4 = new Animator(1000, p => {
  const ty = 100 * (1 - p);
  block.style.transform = `translateY(${ty}px)`;
});

block.addEventListener('click', () => {
  const animators = new AnimationQueue();
  animators.append(a1, a2, a3, a4);
  animators.flush();
});
```

注意到我们的动画队列除了支持 Animator 对象外，还支持普通的函数，因此我们可以组合起来做一些复杂的运动：
[弹跳的小球](https://jsfiddle.net/Amu_xh/41Lwknxs/)

```javascript
const a1 = new Animator(1414, p => {
  const ty = 200 * p * p;
  block.style.transform = `translateY(${ty}px)`;
});

const a2 = new Animator(1414, p => {
  const ty = 200 - 200 * p * (2 - p);
  block.style.transform = `translateY(${ty}px)`;
});

block.addEventListener('click', () => {
  const animators = new AnimationQueue();
  animators.append(a1, a2, function a3() {
    this.append(a1, a2, a3);
  });
  animators.flush();
});
```

还可以再加入更复杂的效果：
[弹跳的小球 - 带阻尼效果]

```javascript
const T = 1414;

const a1 = new Animator(T, function(p) {
  const s = (this.duration * 200) / T;
  const ty = s * (p * p - 1);
  block.style.transform = `translateY(${ty}px)`;
});

const a2 = new Animator(T, function(p) {
  const s = (this.duration * 200) / T;
  const ty = -s * p * (2 - p);
  block.style.transform = `translateY(${ty}px)`;
});

const animators = new AnimationQueue();
function foo() {
  a2.duration *= 0.7;
  if (a2.duration <= 0.0001) {
    console.log('done');
    animators.animators.length = 0;
  }
}

animators.append(a1, foo, a2, function b() {
  a1.duration *= 0.7;
  this.append(a1, foo, a2, b);
});

block.addEventListener('click', function() {
  animators.flush();
});
```
