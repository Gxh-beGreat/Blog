---
title: 深入理解JavaScript错误与调用栈
date: 2017-05-27 8:53:27
categories:
  - 前端知识
tags:
  - JavaScript
---

---

> 探讨错误（Error）和调用栈踪迹（stack trace)以及如何对它们进行操作。

---

原文地址: [JavaScript Errors and Stack Traces in Depth](http://lucasfcosta.com/2017/02/17/JavaScript-Errors-and-Stack-Traces.html)
知乎翻译: [深入理解 JavaScript 错误与调用栈](https://zhuanlan.zhihu.com/p/27127139)

这篇文章主要讲解了

1. [调用栈的工作原理](#调用栈的工作原理)
2. [Error 对象以及错误处理](#Error对象以及错误处理)
3. [操作调用栈踪迹(Error.captureStackTrace 的使用)](#操作调用栈踪迹)

### 调用栈的工作原理

这部分主要了解了 JS 函数调用[栈](https://zh.wikipedia.org/wiki/%E5%A0%86%E6%A0%88)的工作原理,在 JS 中追踪堆栈可使用 `console.trace` 方法

```javascript
// 后执行函数会处于栈顶,执行结束后会被移除,LIFO
function c() {
  console.trace();
}

function b() {
  c();
}

function a() {
  b();
}

a();

// Trace
//   at c (repl:3:9)
//   at b (repl:3:1)
//   at a (repl:3:1)
//   at repl:1:1
//   ....
```

### Error 对象以及错误处理

该部分主要讲解 [Error](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error) 的使用,如何 `throw` 一个异常,如何 `catch`、`finally` 一个异常以及 `Error` 的较好实践(不抛出非`Error`实例异常、传递`Error`实例方式、`Promise`里`reject`错误对象等)。

### 操作调用栈踪迹

该部分主要讲解了 `Error.captureStackTrace` 方法的使用方式(仅 v8 引擎支持)，合理利用这个方法,可以对你的用户隐藏与他无关的框架实现细节

> Error.captureStackTrace(targetObject[, constructorOpt])
> 在 targetObject 中添加一个.stack 属性.对该属性进行访问时,将以字符串的形式返回 Error.captureStackTrace()语句被调用时的代码位置信息(即：调用栈历史),如果传入了第二个参数,传入的函数会被视作调用栈的终点,于是调用栈踪迹就只会显示该函数调执行前的部分.

#### new Error().stack vs Error.captureStackTrace

new Error().stack 的适用范围会更加广泛，而 Error.captureStackTrace 只能在 Node.js 或者 Chrome 上使用。  
在两者都能使用的情况下，优先使用 Error.captureStackTrace，原因为：

- 无需 new 一个新的 Error 对象，节省内存空间，同时代码上也会更加优雅。
- 如果需要忽略部分堆栈信息，使用 Error.captureStackTrace 会更加方便，无需手工操作。
- 如果使用 Error.captureStackTrace,则对于堆栈信息的格式化工作会被延迟至访问 targetObj.stack 时进行，如果 targetObj.stack 未被访问，则堆栈信息的格式化工作会被省略，从而节省计算资源。

```javascript
// 代码示例，引自Chai框架
// `ssf` 代表 "start stack function"。 从这里开始从调用栈里移除不相关信息
function AssertionError(message, _props, ssf) {
  var extend = exclude('name', 'message', 'stack', 'constructor', 'toJSON'),
    props = extend(_props || {});

  // 默认值
  this.message = message || 'Unspecified AssertionError';
  this.showDiff = false;

  // 复制props
  for (var key in props) {
    this[key] = props[key];
  }

  // 一下是我们关注的内容：
  // 如果传入了ssf，就传递给captureStackTrace来移除它之后的内容
  ssf = ssf || arguments.callee;
  if (ssf && Error.captureStackTrace) {
    Error.captureStackTrace(this, ssf);
  } else {
    // 如果没有，就使用原始的stack属性
    try {
      throw new Error();
    } catch (e) {
      this.stack = e.stack;
    }
  }
}
```

如上，如果提供了调用栈开始函数，我们使用 Error.captureStackTrace 来捕捉调用栈然后保存到 AssertionError 实例里，并从调用栈移除不相关内容（都是 Chai 的内部实现细节，只会污染栈信息）。
