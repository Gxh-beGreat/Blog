---
title: 单向链表 - Linked list(一)
date: 2017-07-25 15:50:32
categories:
- CS
tags:
- 数据结构
---
-----

> 使用 Javascript 实现单向链表类

-----

链表存储有序的元素集合，但不同于数组，链表中的元素在内存中并不是连续放置的。每个元素由一个存储元素本身的节点和一个指向下一个元素的引用(也称指针或链接)组成。下图展示了一个链表的结构
![单向链表](https://upload.wikimedia.org/wikipedia/commons/thumb/6/6d/Singly-linked-list.svg/408px-Singly-linked-list.svg.png)
相对传统的数组，链表的一个好处在于，添加或移除元素的时候不需要移动其他元素

### 单向链表的创建
我们将创建一个类来表示单向链表

```javascript
class LinkedList {
    head = null
    length = 0

    Node = function (element) {
        this.next = null
        this.element = element
    }

    // 向列表尾部添加一个新的项
    append (element) {
        let currentNode = this.head
        const node = new this.Node(element)
        if (!currentNode) {
            this.head = node
        } else {
            while (currentNode.next) {
                currentNode = currentNode.next
            }
            currentNode.next = node
        }
        this.length++
    }

    // 从链表中移除特定位置的元素
    removeAt (position) {
        // 判断position的合法性
        if (position < 0 || position >= this.length) return null
        let currentNode = this.head
        if (position === 0) {
            this.head = currentNode.next
        } else {
            let index = 0
            let previousNode = null
            while (index++ < position) {
                previousNode = currentNode
                currentNode = currentNode.next
            }
            previousNode.next = currentNode.next
        }
        this.length--
        return currentNode.element
    }

    // 在任意位置插入一个元素
    insert (position, element) {
        // 判断position的合法性
        if (position < 0 || position > this.length) return false
        let index = 0
        let previousNode = null
        let currentNode = this.head
        const node = new this.Node(element)
        if (position === 0) {
            node.next = currentNode
            this.head = node
        } else {
            while (index++ < position) {
                previousNode = currentNode
                currentNode = currentNode.next
            }
            node.next = currentNode
            previousNode.next = node
        }
        this.length++
        return true
    }

    // 返回传入元素的位置
    indexOf (element) {
        let index = 0
        let currentNode = this.head

        while (currentNode) {
            if (currentNode.element === element) {
                return index
            }
            index++
            currentNode = currentNode.next
        }
        return -1
    }

    // 移除特定元素
    remove (element) {
        const index = this.indexOf(element)
        return this.removeAt(index)
    }

    // 将单向链表转换为一个数组
    toArray () {
        let arr = []
        let currentNode = this.head
        while (currentNode) {
            arr.push(currentNode.element)
            currentNode = currentNode.next
        }
        return arr
    }

    // 清空链表
    clear () {
        this.head = null
        this.length = 0
        return true
    }

    // 将单向链表转换为一个字符串
    toString () {
        return this.toArray().toString()
    }

    // 返回链表是否为空
    isEmpty () {
        return this.length === 0
    }

    // 返回链表长度
    size () {
        return this.length
    }

    // 返回链表head
    getHead () {
        return this.head
    }
}
```
