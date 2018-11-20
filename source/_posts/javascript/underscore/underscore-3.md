---
title:  Underscore源码解析3 — 集合函数
date: 2017-05-26 10:36:18
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

Underscore是一个JavaScript实用库，提供了一整套函数式编程的实用功能，它体积小、能力强，性价比极高，包容部分函数式编程思想。该篇文章将以 *代码 + 注释 + 总结* 的形式对其 _集合函数_ 做分析。

### _each_  Alias: _forEach_
```javascript
// 遍历数组|对象|类数组对象的每个元素，
// 并将其(element, index, list)传入iteratee执行
// 对象则传入(value, key, list)
// 若context不为空，则将iteratee绑定到context身上执行
_.each = _.forEach = function(obj, iteratee, context) {
  iteratee = optimizeCb(iteratee, context);
  var i, length;
  if (isArrayLike(obj)) {
    for (i = 0, length = obj.length; i < length; i++) {
      iteratee(obj[i], i, obj);
    }
  } else {
    var keys = _.keys(obj);
    for (i = 0, length = keys.length; i < length; i++) {
      iteratee(obj[keys[i]], keys[i], obj);
    }
  }
  return obj;
};
```

### _map_  Alias: _collect_
```javascript
// 遍历数组|对象|类数组对象的每个元素
// 将其作为参数传入iteratee(经过cb内置函数转化)
// 最终返回所有的计算结果数组
_.map = _.collect = function(obj, iteratee, context) {
  iteratee = cb(iteratee, context);
  var keys = !isArrayLike(obj) && _.keys(obj),
      length = (keys || obj).length,
      results = Array(length);
  for (var index = 0; index < length; index++) {
    var currentKey = keys ? keys[index] : index;
    results[index] = iteratee(obj[currentKey], currentKey, obj);
  }
  return results;
};
```

### _reduce_ && _reduceRight_
```javascript
function createReduce(dir) {
  function iterator(obj, iteratee, memo, keys, index, length) {
    // 按顺序对每一个元素都执行iteratee，并返回处理后结果，返回最终memo
    for (; index >= 0 && index < length; index += dir) {
      var currentKey = keys ? keys[index] : index;
      memo = iteratee(memo, obj[currentKey], currentKey, obj);
    }
    return memo;
  }

  return function(obj, iteratee, memo, context) {
    iteratee = optimizeCb(iteratee, context, 4);
    var keys = !isArrayLike(obj) && _.keys(obj),
        length = (keys || obj).length,
        index = dir > 0 ? 0 : length - 1;
    // 根据memo传值情况初始化memo
    if (arguments.length < 3) {
      memo = obj[keys ? keys[index] : index];
      index += dir;
    }
    return iterator(obj, iteratee, memo, keys, index, length);
  };
}

 _.reduce = _.foldl = _.inject = createReduce(1);
 _.reduceRight = _.foldr = createReduce(-1);
```
把集合中元素归结为一个单独的数值并返回。`Memo`是`reduce`函数的初始值，`reduce`的每一步都需要由`iteratee`返回。这个迭代传递4个参数：`memo`,`value` 和 迭代的`index`（或者 `key`）和最后一个引用的整个集合数据,`reduceRight`为逆序进行`reduce`

### _find_ Alias: _detect_
```javascript
// 返回第一个通过predicate迭代函数真值检测的元素值，
// 如果没有值传递给测试迭代器将返回undefined。
// 如果找到匹配的元素，函数将立即返回，不会遍历整个list
_.find = _.detect = function(obj, predicate, context) {
  var key;
  if (isArrayLike(obj)) {
    key = _.findIndex(obj, predicate, context);
  } else {
    key = _.findKey(obj, predicate, context);
  }
  if (key !== void 0 && key !== -1) return obj[key];
};
```

### 未完待续
