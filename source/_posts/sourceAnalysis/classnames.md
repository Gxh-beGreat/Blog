---
title: 源码解析 - classnames
date: 2017-05-31 10:35:27
toc: true
categories:
  - 源码解析
tags:
  - JavaScript
  - 源码解析
---

---

> [classnames](https://github.com/JedWatson/classnames) - A simple javascript utility for conditionally joining classNames together

---

[classnames](https://github.com/JedWatson/classnames) 是一个按需生成字符串的库，在 `react` 动态渲染 `className` 时极为有用，下面让我们一起看看 [classnames v2.2.5](https://github.com/JedWatson/classnames/tree/v2.2.5) 源码实现吧

## 结构

`classnames` 提供三种模式：

1. [普通模式](#普通模式-index-js)，对应文件 `/index.js`
2. [`bind` 绑定模式](#绑定模式-bind-js)，该模式会根据绑定对象 `key` 值真否性，判断是否添加对应的 `value` 字符串，可用于 `css modules` 或类似的抽象类名模式，对应文件 `/bind.js`
3. [`dedupe` 去重模式](#去重模式-dedupe-js)，该模式会覆盖重复属性，速度比普通模式慢 _5 倍_ 左右，对应文件 `/dedupe.js`

## 普通模式 - index.js

```javascript
// 用法
classNames('foo', 'bar'); // => 'foo bar'
classNames('foo', { bar: true }); // => 'foo bar'
classNames({ 'foo-bar': true }); // => 'foo-bar'
classNames({ 'foo-bar': false }); // => ''
classNames({ foo: true }, { bar: true }); // => 'foo bar'
classNames({ foo: true, bar: true }); // => 'foo bar'

// lots of arguments of various types
classNames('foo', { bar: true, duck: false }, 'baz', { quux: true }); // => 'foo bar baz quux'

// other falsy values are just ignored
classNames(null, false, 'bar', undefined, 0, 1, { baz: null }, ''); // => 'bar 1'

// Arrays will be recursively flattened as per the rules above:
var arr = ['b', { c: true, d: false }];
classNames('a', arr); // => 'a b c'
```

```javascript
// 核心源码
// index.js
var hasOwn = {}.hasOwnProperty;

function classNames() {
  // 字符串缓存数组
  var classes = [];

  // 遍历传入参数
  for (var i = 0; i < arguments.length; i++) {
    var arg = arguments[i];
    if (!arg) continue;

    // 获取参数类型
    var argType = typeof arg;

    if (argType === 'string' || argType === 'number') {
      // 字符串或数字类型则添加到字符串数组后
      classes.push(arg);
    } else if (Array.isArray(arg)) {
      // 数组类型则引用自身进行递归操作
      classes.push(classNames.apply(null, arg));
    } else if (argType === 'object') {
      // 对象类型则遍历key值，并判断value是否为真，后将真值value的key字符串插入字符串数组内
      for (var key in arg) {
        if (hasOwn.call(arg, key) && arg[key]) {
          classes.push(key);
        }
      }
    }
  }

  // 返回字符串内容
  return classes.join(' ');
}
```

## 绑定模式 - bind.js

```javascript
// 用法
var classNames = require('classnames/bind');

var styles = {
  foo: 'abc',
  bar: 'def',
  baz: 'xyz'
};

var cx = classNames.bind(styles);

var className = cx('foo', ['bar'], { baz: true }); // => "abc def xyz"
```

```javascript
// 核心代码
// bind.js
// 绑定模式与普通模式的区别在于插入字符串时做了 this && this[arg] || arg 的判断
// 此时的this会指向 classNames.bind(obj) 里的 obj 对象
// 若obj[arg]对应value存在，则取此value，否则取arg本身
if (argType === 'string' || argType === 'number') {
  classes.push((this && this[arg]) || arg);
} else if (Array.isArray(arg)) {
  classes.push(classNames.apply(this, arg));
} else if (argType === 'object') {
  for (var key in arg) {
    if (hasOwn.call(arg, key) && arg[key]) {
      classes.push((this && this[key]) || key);
    }
  }
}
```

## 去重模式 - dedupe.js

```javascript
// 用法
var classNames = require('classnames/dedupe');

classNames('foo', 'foo', 'bar'); // => 'foo bar'
classNames('foo', { foo: false, bar: true }); // => 'bar'
```

```javascript
// 核心代码
// dedupe.js
// dedupe版本主要思想是：实例化一个空对象接收传参的字符串值，
// 字符串作为key，value为布尔值，添加则为true，不保留则为false，
// 这样便能达到后者覆盖前者的功能

// IIFE，隔离作用域
var classNames = (function() {
  // 通过Object.create(null)生成空对象，去除 {} 中继承 Object 属性对程序的影响，省去了后面对对象做 hasOwnPropertyhas 的检查
  // http://stackoverflow.com/questions/15518328/creating-js-object-with-object-createnull#answer-21079232
  function StorageObject() {}
  StorageObject.prototype = Object.create(null);

  // 数组解析
  function _parseArray(resultSet, array) {
    var length = array.length;

    for (var i = 0; i < length; ++i) {
      // 对每一项数据进行解析
      _parse(resultSet, array[i]);
    }
  }

  var hasOwn = {}.hasOwnProperty;

  // 数字解析
  function _parseNumber(resultSet, num) {
    resultSet[num] = true;
  }

  // 对象解析
  function _parseObject(resultSet, object) {
    for (var k in object) {
      if (hasOwn.call(object, k)) {
        // 对不需要添加的属性设置布尔值，而不是选择delete掉改key
        // https://www.smashingmagazine.com/2012/11/writing-fast-memory-efficient-javascript/#de-referencing-misconceptions
        resultSet[k] = !!object[k];
      }
    }
  }

  var SPACE = /\s+/;
  // 字符串解析
  function _parseString(resultSet, str) {
    var array = str.split(SPACE);
    var length = array.length;

    for (var i = 0; i < length; ++i) {
      resultSet[array[i]] = true;
    }
  }

  // 按不同类型区分解析
  function _parse(resultSet, arg) {
    if (!arg) return;
    var argType = typeof arg;

    // 'foo bar'
    if (argType === 'string') {
      _parseString(resultSet, arg);

      // ['foo', 'bar', ...]
    } else if (Array.isArray(arg)) {
      _parseArray(resultSet, arg);

      // { 'foo': true, ... }
    } else if (argType === 'object') {
      _parseObject(resultSet, arg);

      // '130'
    } else if (argType === 'number') {
      _parseNumber(resultSet, arg);
    }
  }

  function _classNames() {
    // 通过新变量缓存 arguments，以保证 arguments 不被传递或暴露
    // https://github.com/petkaantonov/bluebird/wiki/Optimization-killers#32-leaking-arguments
    var len = arguments.length;
    var args = Array(len);
    for (var i = 0; i < len; i++) {
      args[i] = arguments[i];
    }

    var classSet = new StorageObject();
    _parseArray(classSet, args);

    var list = [];

    // 遍历缓存实例，将true值key插入到字符串数组内
    for (var k in classSet) {
      if (classSet[k]) {
        list.push(k);
      }
    }

    // 返回字符串
    return list.join(' ');
  }

  return _classNames;
})();
```

## 兼容性

因为代码使用了 `Array.isArray`，故不支持 ie8 以下浏览器，若需要使用，则需要自行添加对应的 [`Array.isArray Polyfill`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray?v=example)，而原文档里面提到代码还使用了 `Object.keys`，但是在该最新版本中暂未发现次方法，贴上对应 [`Object.keys Polyfill`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)
