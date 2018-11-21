---
title: Promise - 告别无尽回调
date: 2017-05-25 09:05:06
categories:
  - 前端知识
tags:
  - JavaScript
---

---

> 跟无尽回调 say bye bye~

---

### 介绍

本文将参考 [JavaScript Promise 迷你书](http://liubin.org/promises-book/#)，以 [ES6 Promises](https://tc39.github.io/ecma262/#sec-promise-objects) 规范，即 JavaScript 的标准规范为基础的 `Promise` 的相关内容及使用方法。

#### 什么是 Promise

`Promise` 是抽象异步处理对象以及对其进行各种操作的组件，它能以优雅的方式实现异步操作，看看不使用 `Promise`，实现回调的情况是如何

```javascript
// fn、fn1、fn2、fn3方法均接受一个回调函数
// 需要的执行顺序为 fn => fn1 => fn2 => fn3
// 接受回调吧！ BOOM ！！！ 😨😨
fn(
  fn1(
    fn2(
      fn3(
        ...
      )
    )
  )
)
```

然后我们再来看下使用 `Promise` 实现会如何

```javascript
// 瞬间清爽~~ 🍉🍉🍉🍉
fn()
  .then(fn1())
  .then(fn2())
  .then(fn3());
```

`Promise` 在解决无尽回调之外，更大的作用是方便我们去控制我们异步使用，虽仅有几个 API，但也能非常自由的控制我们的异步逻辑。

### Promise 实践

#### 创建 promise 对象

创建 `promise` 有下面几种方式：

1. 实例化 `Promise` 对象:`const promise = new Promise((resolve, reject) => {});`
1. `Promise.resolve()`
1. `Promise.reject()`
1. `Promise.all([])`
1. `Promise.race([])`

本文将以第一点的 `Promise实例` 为例子进行展开学习，剩下的均为 `Promise` 的静态方法，使用方式在 `实例` 的基础上有所拓展，具体的使用方式可参考 [Promises API Reference](http://liubin.org/promises-book/#promise-api-reference)。

#### resolve, reject

在上面创建过程中，我们在实例化时传入了一个回调函数，回调函数接受了两个参数:`resolve`、`reject`，这两个小家伙有什么作用呢？让我们先看看下面这个例子。

```javascript
function timeout(ms) {
  return new Promise((resolve, reject) => {
    if (ms < 100) reject(new Error('延时需要大于100ms'));
    setTimeout(() => {
      resolve('done');
    }, ms);
  });
}

timeout(1000)
  .then(msg => console.log(msg))
  .catch(err => console.log(err));
// 1000ms后输出 done

timeout(50)
  .then(msg => console.log(msg))
  .catch(err => console.log(err));
// Error: 延时需要大于100ms
```

当 `promise` 被 `resolve` 的时候，将触发 `onFulfilled` 事件，此时将告知 `promise` 可以往下执行 `then` 方法了，`resolve` 的参数将作为 `then` 方法里面函数的参数，同理, `reject` 将触发 `onRejected` 事件，告知执行 `catch` 方法，参数传递方式一样。  
![resolve、reject](http://liubin.org/promises-book/Ch1_WhatsPromises/img/promise-onFulfilled_onRejected.png)  
所以 `promise` 更像是定义了一套钩子(hook) 函数，可以自主的控制什么时候讲进行下一步流程函数的执行。

#### then/catch 方法将返回新创建的 Promise 对象

`then/catch` 方法将返回新创建的 `Promise` 对象,因此我们可以根据这个特性进行一连串的方法连的调用。  
![then/catch](http://liubin.org/promises-book/Ch2_HowToWrite/img/then_catch.png)

```javascript
// 上行函数的输出将作为下行函数的输入，同 gulp pipe
const promise = new Promise(resolve => {
  resolve(100);
});
promise
  .then(value => {
    return value * 2;
  })
  .then(value => {
    return value * 2;
  })
  .then(value => {
    console.log('2: ' + value); // => 100 * 2 * 2
  });
```

### Deferred and Promise

使用过 Jquery Deferred 的各位应该都对 `Deferred` 不陌生了，`Deferred` 和 `Promise` 不同，它没有共通的规范，每个 Library 都是根据自己的喜好来实现的。

#### Deferred 和 Promise 的关系

简单来说，`Deferred` 和 `Promise` 具有如下的关系

- `Deferred` 拥有 `Promise`
- `Deferred` 具备对 `Promise` 的状态进行操作的特权方法  
  ![Deferred/Promise](http://liubin.org/promises-book/Ch4_AdvancedPromises/img/deferred-and-promise.png)

#### Deferred 状态操作特权

`Deferred` 允许将 `Promise` 实例化到一个变量上，并赋予这个变量 `resolve` 及 `reject` 方法，让其在任何时刻都可以改变 Promise 状态（`Promise` 需要在参数回调内才可改变）

```javascript
function timeout(ms) {
  const deferred = new Deferred();
  if (ms < 100) deferred.reject(new Error('延时需要大于100ms'));
  setTimeout(() => {
    deferred.resolve('done');
  }, ms);
}
```

#### Deferred 实现

基于 `Promise` 实现 `Deferred` 还是比较容易理解的

```javascript
function Deferred() {
  this.promise = new Promise(
    function(resolve, reject) {
      this._resolve = resolve;
      this._reject = reject;
    }.bind(this)
  );
}
Deferred.prototype.resolve = function(value) {
  this._resolve.call(this.promise, value);
};
Deferred.prototype.reject = function(reason) {
  this._reject.call(this.promise, reason);
};
```

### 结束语

差不多是这样了~下次更新 `async/await` 使用
