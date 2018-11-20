---
title: Underscore源码解析2 — 内置函数
date: 2017-05-26 09:18:41
toc: true
categories:
- JavaScript
tags:
- JavaScript
- Underscore
- 源码分析
---
-------

> 本系列将对 [Underscore v1.8.3](https://github.com/jashkenas/underscore/tree/1.8.3) 进行源码解读，并通过部分测试用例作进一步分析，更多可访问 [Underscore 源码解读](/tags/Underscore/)

-------

Underscore是一个JavaScript实用库，提供了一整套函数式编程的实用功能，它体积小、能力强，性价比极高，包容部分函数式编程思想。该篇文章将以 *代码 + 注释 + 总结* 的形式对其 _内置函数_ 做分析。

### 回调优化 - optimizeCb
```javascript
/**
 * 以修改函数的执行环境跟限制传参序列、数量的形式优化回调函数
 * @param  {Function} func     回调函数
 * @param  {Object} context   上下文
 * @param  {Number} argCount  参数长度
 * @return {Function}         新的callback
 */
var optimizeCb = function(func, context, argCount) {
  if (context === void 0) return func;
  switch (argCount == null ? 3 : argCount) {
    case 1: return function(value) {
      return func.call(context, value);
    };
    case 2: return function(value, other) {
      return func.call(context, value, other);
    };
    case 3: return function(value, index, collection) {
      return func.call(context, value, index, collection);
    };
    case 4: return function(accumulator, value, index, collection) {
      return func.call(context, accumulator, value, index, collection);
    };
  }
  return function() {
    return func.apply(context, arguments);
  };
};
```
`optimizeCb`主要通过对回调函数`func`绑定上下文`context`的方式实现优化，其可以让解析器在解析`func`时，不需要一层层追溯`func`的上下文，在调用次数达到一定量时，该优化还是有意义的。

### 回调分类 - cb
```javascript
var cb = function(value, context, argCount) {
  // 空类型，返回一个返回vuale的默认迭代器
  if (value == null) return _.identity;
  // function类，对回调函数做优化
  if (_.isFunction(value)) return optimizeCb(value, context, argCount);
  // Object类，返回属性匹配器
  if (_.isObject(value)) return _.matcher(value);
  // 其余情况。返回属性访问器
  return _.property(value);
};
```
针对`value`的类型不同，返回不同的回调函数，或为默认迭代器([*_.identity*]())，任意回调([*optimizeCb*](#optimizeCb))，属性匹配器([*_.matcher*]())或属性访问器([*_.property*]()) _todo_

### 属性分配器 - createAssigner
```javascript
/**
 * 属性分配器
 * @param  {Function} keysFunc     object属性读取方法(_.keys | _.allKeys)
 * @param  {Boolean} undefinedOnly 是否只分配原object里undefined的属性 true/false
 * @return {Function}              
 */
var createAssigner = function(keysFunc, undefinedOnly) {
  return function(obj) {
    var length = arguments.length;
    // 参数长度小于2或obj为null，则返回原参数
    if (length < 2 || obj == null) return obj;
    // 遍历所有参数属性，并进行分配
    for (var index = 1; index < length; index++) {
      var source = arguments[index],
          keys = keysFunc(source),
          l = keys.length;
      for (var i = 0; i < l; i++) {
        var key = keys[i];
        if (!undefinedOnly || obj[key] === void 0) obj[key] = source[key];
      }
    }
    return obj;
  };
};
```
遍历属性并插入到原obj里，可用于合并对象或拷贝对象

### 类式继承 - baseCreate
```javascript
/**
 * 类式继承，返回一个新的对象
 * @param  {Object} prototype 原型对象
 * @return {Object}           新对象
 */
var baseCreate = function(prototype) {
  if (!_.isObject(prototype)) return {};
  if (nativeCreate) return nativeCreate(prototype);
  Ctor.prototype = prototype;
  var result = new Ctor;
  Ctor.prototype = null;
  return result;
};
```
[Object.create](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/create) 的兼容性写法

### 属性获取器 - property
```javascript
/**
 * 属性获取器
 * @param  {String} key   需要获取的key值
 * @return {Function}     获取obj特定属性的函数
 */
var property = function(key) {
  return function(obj) {
    return obj == null ? void 0 : obj[key];
  };
};
```

### 类数组判断 - isArrayLike
```javascript
 var MAX_ARRAY_INDEX = Math.pow(2, 53) - 1;
 var getLength = property('length');
 var isArrayLike = function(collection) {
   var length = getLength(collection);
   return typeof length == 'number' && length >= 0 && length <= MAX_ARRAY_INDEX;
 };
```
用来判断 `collection` 是否为类数组，从而选择数组或对象形式对其做对应迭代操作

### 未完待续
代码分析是根据代码结构顺序依次往下，大致翻阅发现后面还有一些内置函数，故将跟随系列持续更新该篇文章 _todo_
