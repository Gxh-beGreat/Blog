---
title: DevTools 小技巧 day 5 —— 诡异的 console.log
date: 2018-12-10 10:13:20
categories:
  - 效率神器
tags:
  - DevTools
  - 文章翻译
---

## 4-4

> [DevTools tips — day 5: the curious case of console.log](https://medium.com/@tomsu/devtools-tips-day-5-the-curious-case-of-console-log-36bc7e27a97f)

---

在圣诞节 🎄 前的 24 天里，我将通过短篇幅的形式为大家分享更高效有趣的 `DevTools` 使用技巧。

## 16. 记录的对象并非预期值

`console.log` 对于 `对象` 的输出方式，多少都让我们在调试的时候困惑过或是踩到坑：

> 通过控制台输出的对象值，将通过引用的方式进行存储的，开发者展开查看到的都将会是对象的最新属性值

这意味着如果你是记录一个对象，修改其属性值并再次 log，查看控制台，你将看到第一个日志（修改之前）...与第二个日志具有相同的值！

下面的动图会展示这种情况：
![](/images/DevTools小技巧/5-1.gif)

现在想象一下，当你试图对比一个对象属性修改前后的值是否符合要求的时候，这个问题就很伤脑筋了~ 🤯

那么这种情况要怎么处理呢？好吧，你可能需要记录一个对象的副本（一个新的引用）或......在 `serious debugging`（译者理解为 Sources 面板中的断点代码调试） 中使用 断点 和 Sources 面板代替！
