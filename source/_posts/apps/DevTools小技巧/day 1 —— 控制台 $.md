---
title: DevTools 小技巧 day 1 —— 控制台 $
date: 2018-12-06 15:53:27
categories:
  - 效率神器
tags:
  - DevTools
  - 文章翻译
---

---

> [DevTools tips — day 1: the console dollars](https://medium.com/@tomsu/devtools-tips-day-1-the-console-dollars-3aa0d93e923c)

---

在圣诞节 🎄 前的 24 天里，我将通过短篇幅的形式为大家分享更高效有趣的 `DevTools` 使用技巧，现在就让我们开始吧！

## 1. `$0`

`$0` 是对当前 Elements 面板中选定的 html 节点的引用  
`$1` 则是对上一次选定的节点的引用，`$2` 是对上上一次选定的引用，以此类推，最多可支持到 `$4`  
你可以使用这些引用变量来进行对用的操作（例如：`$1.appendChild($0)`）。
![](/images/DevTools小技巧/1-1.gif)

## 2. `$ and $$`

控制台里的 `$` 是冗长方法 `document.querySelector` 的别名。  
使用的前提条件是你的应用中没有声明 `$` 变量（例如： JQuery）。

`$$`更是个省时利器了 🚀，它不仅会执行 `document.QuerySelectorAll` 方法，而且会将节点以数组的形式返回，而非 `NodeList` 类型。

通常情况下：`Array.from(document.querySelectorAll('div')) === $$('div')`  
超便捷有木有！！🚀
![](/images/DevTools小技巧/1-2.gif)

## 3. `$_`

`$_` 是上一次计算的表达式结果的引用。
![](/images/DevTools小技巧/1-3.png)

## 4. `$i`

通过 Chrome 的 [Console Importer](https://chrome.google.com/webstore/detail/console-importer/hgajpakhafplebkdljleajgbpdmplhie) 扩展程序，我们可以快捷的在控制台导入并使用 `npm`。
只需执行 `$i('lodash') or $i('moment')`，几秒之后 🕙，我们便可以获取到 `lodash/moment` 啦！

![](/images/DevTools小技巧/1-4.gif)

以上就是今天到全部内容啦。Short and sweet.🍬
