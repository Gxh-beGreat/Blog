---
title: DevTools 小技巧 day 3 —— console 方法
date: 2018-12-09 11:43:27
categories:
  - 效率神器
tags:
  - DevTools
  - 文章翻译
---

---

> [DevTools tips — day 3: console methods](https://medium.com/@tomsu/devtools-tips-day-3-console-methods-783791e91990)

---

在圣诞节 🎄 前的 24 天里，我将通过短篇幅的形式为大家分享更高效有趣的 `DevTools` 使用技巧。我们在[上一篇](2018/12/08/apps/DevTools小技巧/day%202%20——%20复制&保存/)中已经介绍到了第 8 种技巧，那么今天我们将从第 9 开始。

## 9. console.assert

![出自: https://developer.mozilla.org/en-US/docs/Web/API/console/assert](/images/DevTools小技巧/3-1.png)

`console.assert` 方法第一个参数（断言）为 `false` 时，则将一个错误消息写入控制台，如果断言是 true，没有任何反应。  
这个方法对于在特定条件下记录消息时很有用，你可以在不书写 `if` 语句的情况下执行此操作，而且你还能从这里得到对应的堆栈跟踪 😁

![](/images/DevTools小技巧/3-2.gif)

## 10.console.table

这是一个鲜为人知的方法。  
如果你有一个数组（或类似数组的对象，或者只是一个对象），你可以使用 `console.table` 方法在控制台中以一个漂亮的表的形式打印它。  
它不仅会基于数据所包含的属性来计算表列名称，而且列也可以调整大小甚至可排序！😳  
如果列太多，你还可以使用第二个参数，传入需要显示的列名数组。

![](/images/DevTools小技巧/3-3.gif)

> 哦！如果你还同时涉猎 nodejs 后端，那么恭喜你！10.0 版本之后，你也可以使用 `console.table` 啦!

## 11. console.dir

最常使用的 `console.log` 方法，在大多数情况下，都以最合适的格式化数据输出给开发人员，但有时它可能不是你想要看到的 —— 典型的例子是打印出一个 DOM 节点。
`console.log` 将呈现此交互式元素，它看起来就像刚从 `Elements` 面板中剪切出来一样。  
如果要检查它引用的实际 JavaScript 对象，该怎么办，查看它的属性等呢？
在这种情况下，如果您需要更多信息描述数据，请改用 `console.dir`。

![](/images/DevTools小技巧/3-4.gif)

这就是今天所有的内容啦！😉
