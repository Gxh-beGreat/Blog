---
title: JavaScript 最新特性实现的三大黑科技
date: 2017-06-19 08:50:32
toc: true
categories:
  - 前端知识
tags:
  - JavaScript
---

---

> [《JavaScript 最新特性实现的三大黑科技》](https://www.h5jun.com/post/three-black-tech-in-modern-js.html?utm_source=tuicool&utm_medium=referral) 读后总结

---

### 依次执行多项异步任务

<a class="jsbin-embed" href="//code.h5jun.com/dep/1/embed?console">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

```javascript
async function taskReducer(promise, action) {
  let res = await promise;
  return action(res);
}

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

async function asyncTask(i) {
  await sleep(500);
  console.log(`task ${i} done`);
  return ++i;
}

[asyncTask, asyncTask, asyncTask].reduce(taskReducer, 0);
```

`taskReducer` 的两个参数是 `promise` 和 `action`，`promise` 是代表当前任务的 `promise`，而 `action` 是下一个要执行的任务。我们可以 `await` 当前 `promise` 执行当前任务，然后将执行结果传给下一个 `action` 就可以了。

### generator 与 async/await 一同使用

<a class="jsbin-embed" href="//code.h5jun.com/woge/2/embed?console">JS Bin on jsbin.com</a><script src="https:////code.h5jun.com/js/embed.min.js?3.40.2"></script>

```javascript
async function reducer(promise, action) {
  let res = await promise;
  return action(res);
}

function tick(i) {
  console.log(i);
  return new Promise(resolve => setTimeout(() => resolve(++i), 1000));
}

function continuous(...functors) {
  return async function(input) {
    return await functors.reduce(reducer, input);
  };
}

function* timing(count = 5) {
  for (let i = 0; i < count; i++) {
    yield tick;
  }
}

continuous(...timing(10))(0);
```

这部分没明白这么使用的优势在哪，`generator` 使用部分其实就是讲 `tick` 函数装入一个数组内，后使用 `continuous` 方法对其进行 `reduce`

```javascript
// 将timing改为这样也可以运行，所以不明白使用 generator 的优势在哪
function timing(count = 5) {
  const arr = [];
  for (let i = 0; i < count; i++) {
    arr.push(tick);
  }
  return arr;
}
```

### 使用 Proxy 实现 PHP 中的常用“魔术方法”

因为之前并没有对 `Proxy` 有过了解，所以另开了一篇文章对其做了下小总结 [ES6 - 初识 Proxy](/2017/06/20/javascript/ES6-初识Proxy/)  
文章例子在对象构造的时候，分别“代理”对象实例的属性 get 和 set 方法，如果对象上已存在某个属性或方法，代理直接返回或操作该属性。否则，判断对象上是否有 `__get`、`__set` 或者 `__call` 方法，有的话，做相应的处理相当于是使用 `Proxy` “重载”对象的属性和方法读写。  
这种加工并返回新类的方式，有函数式编程的感觉，我们也可以使用 `修饰器 Decorators` 来对其封装

```javascript
@magical
class Foo {
    __call(key, ...args){
        ...
    }
}
```

之前写过相关文章 [ES7 修饰器](/2017/05/29/javascript/ES7修饰器/)

以上就是对 [《JavaScript 最新特性实现的三大黑科技》](https://www.h5jun.com/post/three-black-tech-in-modern-js.html?utm_source=tuicool&utm_medium=referral) 文章的读后感，谢谢原作者的分享。
