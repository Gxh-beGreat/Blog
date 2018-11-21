---
title: 栈 - stack
date: 2017-07-22 11:10:32
categories:
  - 算法
tags:
  - 数据结构
---

---

> 使用 Javascript 实现栈类

---

_栈_ 是一种遵从 _后进先出(LIFO)_ 原则的有序集合，新添加的或待删除的元素都保存在栈的末尾，称作 _栈顶_，另一端就叫 _栈底_，在栈里，新元素都靠近栈顶，旧元素都靠近栈底。

### 栈的创建

我们将创建一个类来表示栈

```javascript
class Stack {
  items = [];

  // 添加一个(或几个)新元素到栈顶
  push(...args) {
    this.items.push(...args);
  }

  // 移除栈顶元素，同时返回被移除的元素
  pop() {
    return this.items.pop();
  }

  // 返回栈顶的元素内容
  peek() {
    const { items } = this;
    return items[items.length - 1];
  }

  // 判断栈是否为空
  isEmpty() {
    return this.items.length === 0;
  }

  // 移除栈里的所有元素
  clear() {
    this.items = [];
  }

  // 返回栈元素个数
  size() {
    return this.items.length;
  }

  // 输出items元素
  print() {
    console.log(this.items.toString());
  }
}
```

### 将十进制转化为其他进制

创建了 `Stack` 类，让我们用其写一个小 demo，写一个将十进制转为其他进制的方法:

```javascript
function baseConverter(decNum, base) {
  let rem = null;
  let baseString = '';
  let remStack = new Stack();
  let digits = '0123456789ABCDEF';

  while (decNum > 0) {
    rem = Math.floor(decNum % base);
    remStack.push(rem);
    decNum = Math.floor(decNum / base);
  }

  while (!remStack.isEmpty()) {
    baseString += digits[remStack.pop()];
  }

  return baseString;
}
```
