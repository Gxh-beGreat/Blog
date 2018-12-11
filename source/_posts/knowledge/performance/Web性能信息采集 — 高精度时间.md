---
title: Web性能信息采集 — 高精度时间
date: 2018-11-22  10:09:27
categories:
  - 前端知识
tags:
  - JavaScript
  - 性能优化
---

---

> [2018 你应该知道的 Web 性能信息采集指南](https://github.com/berwin/Blog/issues/25) - 如何获得高精度的时间？

---

### 如何获得高精度的时间（摘录）

ECMA-262 规范中定义了 `Date` 对象来表示自 1970 年 1 月 1 日以来的毫秒数。它足以满足大部分需求，但缺点是时间会受到时钟偏差与系统时钟调整的影响。时间的值不总是单调递增，后续值有可能会减少或者保持不变。

例如，下面这段代码计算出来的“`duration`”有可能被记录为正数、负数或零。

```js
const mark_start = Date.now();
doTask(); // Some task
const duration = Date.now() - mark_start;
```

上面这段代码获取的持续时间“duration”并不精准，它会受到时钟偏差与系统时钟调整的影响，所以最终得到的“duration”可能为正数、负数或零，我们根本不知道它记录的时间究竟是不是正确的时间。

高精度时间（High Resolution Time，简称`hr-time`）规范定义了`Performance`对象，通过`Performance`对象我们可以获得高精度的时间。

`Performance`对象包含方法`now`和属性`timeOrigin`：

- 方法`now`被执行后会返回从 `timeOrigin` 到现在的高精度时间。
  > _当前时间 - performance.timeOrigin_
- 属性`timeOrigin`返回[页面浏览上下文第一次被创建](https://html.spec.whatwg.org/multipage/browsers.html#creating-a-new-browsing-context)的时间。如果全局对象为`WorkerGlobalScope`，那么`timeOrigin`为 worker 被创建的时间。

  > timeOrigin 的时间值不受时钟偏差与系统时钟调整的影响。

  例如，当`timeOrigin`的值被确定之后，无论将系统时间设置到什么时间，下面代码始终返回`timeOrigin`最初被赋予的时间：

  ```js
  new Date(performance.timeOrigin).toLocaleString();
  // 2018/8/6 上午11:41:58
  ```

如果两个时间值拥有相同的时间起源（[Time Origin](https://w3c.github.io/hr-time/#dfn-time-origin)），那么使用 `performance.now` 方法返回的任意两个按时间顺序记录的时间值之间的差值永远不可能是负数。

例如，下面这段代码计算出来的“`duration`”永远不可能为负数。

```js
const mark_start = performance.now();
doTask(); // Some task
const duration = performance.now() - mark_start;
```

通过`performance.timeOrigin` + `performance.now` 可以得到精准的当前时间。该时间不受时钟偏差与系统时钟调整的影响。

> 不受时钟偏差与系统时钟调整的影响指的是当`timeOrigin`的值被确定之后修改了系统时间，这时候`timeOrigin`不会受到影响。

```js
const timeStamp = performance.timeOrigin + performance.now();
console.log(timeStamp); // 1533539552977.5718
new Date(timeStamp).toLocaleString();
// "2018/8/6 下午3:10:42"
```

### 场景使用

高精度时间 `performance.now`，想到的场景使用有以下几个：

1. 可以用于微秒级动画的效果的使用上，具体使用方式可参考 [实现 JavaScript 动画序列播放](/2017/06/22/knowledge/实现JavaScript动画序列播放/#performance-now-获取当前时间)
2. `Web端时钟`，获取服务器准确时间作为基准值，根据 `performance.now` 计算 `duration`，虽然有网络请求时间误差，但是可以避免受到系统时间调整带来的影响

```js
const serverTime = (await getServerTime()) - performance.now();
let nowTime;
setInterval(
  (function getRealTime() {
    nowTime = serverTime + performance.now();
    return getRealTime;
  })(),
  1000
);
```

3. `活动/商品购买倒计时`，同第二点类似，均已服务器时间为基准值，计算距离结束时间的 `duration`

```js
const { serverStartTime, serverEndTime } = await getServerTime();
const nowTime = performance.now();
const serverDurationForNowToEnd = serverEndTime - serverStartTime;
let realDurationForNowToEnd = 0;
setInterval(
  (function getRealDuration() {
    realDurationForNowToEnd =
      serverDurationForNowToEnd - performance.now() - nowTime;
  })(),
  1000
);
```
