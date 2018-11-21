---
title: Underscore源码解析1 — 基础配置
date: 2017-05-25 10:35:27
toc: true
categories:
  - 源码解析
tags:
  - JavaScript
  - 源码解析
---

---

> 本系列将对 [Underscore v1.8.3](https://github.com/jashkenas/underscore/tree/1.8.3) 进行源码解读，并通过部分测试用例作进一步分析，更多可访问 [Underscore 源码解读](/tags/Underscore/)

---

Underscore 是一个 JavaScript 实用库，提供了一整套函数式编程的实用功能，它体积小、能力强，性价比极高，源码长度只需要 1000 多行，最近刚好在学习 _函数式编程_，所以选择了 _Underscore_ 作为博客开篇第一系列。该篇文章将以 _代码 + 注释 + 总结_ 的形式对其 _基础配置_ 做分析。

### IIFE 隔离作用域

```javascript
(function() {
  ...
}.call(this));
```

通过 IIFE 隔离作用域，防止全局污染，通过 _call_ 将作用域 _this_ 指向当前环境(浏览器端为 _window_，nodejs 环境指向 _exports_)

### 初始化配置

```javascript
// 创建root对象，用来接收this指向，this内容参考上级说明
var root = this;

// 通过previousUnderscore变量缓存环境中之前的 _ 属性
var previousUnderscore = root._;

// 将内置对象的原型链缓存在局部变量, 方便快速调用，缩小文件体积
var ObjProto = Object.prototype,
  ArrayProto = Array.prototype,
  FuncProto = Function.prototype;

// 将内置对象原型中的常用方法缓存在局部变量, 方便快速调用
var push = ArrayProto.push,
  slice = ArrayProto.slice,
  toString = ObjProto.toString,
  hasOwnProperty = ObjProto.hasOwnProperty;

// 缓存将使用到的ES5方法
// 如果宿主环境中支持这些方法则优先调用, 如果宿主环境中没有提供, 则会由Underscore实现
var nativeIsArray = Array.isArray,
  nativeKeys = Object.keys,
  nativeBind = FuncProto.bind,
  nativeCreate = Object.create;

// 创建空的构造函数，用于做prototype交换/替换操作时缓存属性
var Ctor = function() {};

// 重新声明安全的_对象（覆盖全局_对象），并提供给下面代码使用
var _ = function(obj) {
  if (obj instanceof _) return obj;
  if (!(this instanceof _)) return new _(obj);
  this._wrapped = obj;
};

// 针对不同的宿主环境, 将Undersocre的命名变量存放到不同的对象中
if (typeof exports !== 'undefined') {
  if (typeof module !== 'undefined' && module.exports) {
    exports = module.exports = _;
  }
  exports._ = _;
} else {
  root._ = _;
}

// 版本声明
_.VERSION = '1.8.3';
```

1. 缓存变量，达到不污染原数据、清晰化代码、缩小文件体积等作用
2. 通过空构造函数实现 _prototype_ 的替换操作
3. 重新声明 \_\__ 变量，防止恶意攻击，_\__ 函数内部的方法暂未明白有何作用，\_todo_
4. 针对不同的宿主环境, 将命名变量存放到不同的对象中(_exports_ or _global variable_)

### 总结

通过基本配置我们了解到了框架开发的一些基础知识，包括作用域隔离、安全防护、框架导出等，下一期我们将从 _Undersocre_ 的一些内置函数下手，体验函数式编程的快感~
