---
title: DevTools 小技巧 day 4 —— Elements 面板
date: 2018-12-10 10:13:20
categories:
  - 效率神器
tags:
  - DevTools
  - 文章翻译
---

## 4-4

> [DevTools tips — day 4: the Elements panel](https://medium.com/@tomsu/devtools-tips-day-4-console-methods-47c854782cef)

---

在圣诞节 🎄 前的 24 天里，我将通过短篇幅的形式为大家分享更高效有趣的 `DevTools` 使用技巧。今天我们将讲一些关于 Elements 面板的使用小技巧

## 12. 使用 ”h“ 隐藏元素

只需按 `h` 即可隐藏在 Elements 面板中选中的元素。再次按 `h` 使其重新出现。  
这对于网页截图，且需要隐藏部分敏感性数据时很有效

![](/images/DevTools小技巧/4-1.gif)

## 13. 拖放元素

如果想要检查 html 的节点在 DOM 树的其他位置会是怎样的情况，只需使用鼠标将其拖放到对应位置，就像你在电脑上拖放文件一样:-)

![](/images/DevTools小技巧/4-2.gif)

## 14. …或使用键盘控制！

如果你想在 DOM 结构上向下或向下移动当前选中的元素，而不是拖放，你也可以使用 `[ctrl] + [⬆]`/ `[ctrl] + [⬇]`（Mac 下使用`[⌘] + [⬆]` / `[⌘] + [⬇]`）。

![](/images/DevTools小技巧/4-3.gif)

## 15. 它等同于一个编辑器

我们可以在 Elements 面板里进行拖放，编辑，复制（是的，也可以使用 `[ctrl] + [v]` 进行粘贴），一旦我们弄乱了 html 结构，不需要担心，在任何文本/图形编辑器中的标准是什么：

- 使用 `[ctrl] + [z]`（Mac 上的 `[⌘] + [z]`）撤消上一步操作
- 使用 `[ctrl] + [shift] + [z]` 重做撤销的操作

![](/images/DevTools小技巧/4-4.gif)
