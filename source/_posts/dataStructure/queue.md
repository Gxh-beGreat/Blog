---
title: 队列 - queue
date: 2017-07-23 11:50:32
categories:
- CS
tags:
- 数据结构
---
-----

> 使用 Javascript 实现队列类

-----

_队列_ 是遵循 _先进先出(FIFO)_ 原则的一组有序的项，队列在尾部添加新元素，并从顶部移除元素，最新添加的元素必须排在队列的末尾

### 队列的创建
我们将创建一个类来表示队列

```javascript
class Queue {
    items = []

    // 向队列尾部添加一个(或多个)新的项
    enqueue (...args) {
        this.items.push(...args)
    }

    // 移除队列中的第一项,并返回移除的元素
    dequeue () {
        return this.items.shift()
    }

    // 返回队列中的第一个元素
    front () {
        return this.items[0]
    }

    // 判断队列是否为空
    isEmpty () {
        return this.items.length === 0
    }

    // 移除队列里的所有元素
    clear () {
        this.items = []
    }

    // 返回队列元素个数
    size () {
        return this.items.length
    }

    // 输出items元素
    print () {
        console.log(this.items.toString())
    }
}
```

### 优先队列的创建
优先队列想比较于普通队列，它的每个元素均有一个优先权值，权值越大，优先级越前

```javascript
    class PriorityQueue {
        items = []

        enqueue (element, priority) {
            const { items, isEmpty, size } = this
            if (isEmpty()) return items.push({element, priority})
            for (let i = 0; i < size(); i++) {
                if (priority < items[i].priority) {
                    return items.splice(i, 0, {element, priority})
                }
            }
            items.push({element, priority})
        }

        // 其他方法和普通Queue实现相同
    }
```
