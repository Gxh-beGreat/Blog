---
title: ES7修饰器
date: 2017-05-29 23:06:32
categories:
  - 前端知识
tags:
  - JavaScript
---

---

> ES7 修饰器(Decorator) 初认识，文章参考阮一峰老师的 [ES6 标准入门](http://es6.ruanyifeng.com/)

---

### 何为修饰器

修饰器(`Decorator`)是一个表达式，用于修改类的行为，这是 `ES7` 的一个[提案](https://github.com/wycats/javascript-decorators)，所以在 `ES7` 规范出来之前，它的正式性还不能得到确定，但如今在许多 Node 、React 等项目中已经使用该方案，目前 Babel 转码器已经支持修饰器的使用。  
修饰器对类的行为的改变，是在代码编译时发生的，而不是在运行时，这意味着修饰器能在编译阶段运行代码

```javascript
function testable(target) {
  target.isTestable = true;
}

@testable
class MyTestableClass {}

console.log(MyTestableClass.isTestable); // true
```

上面的代码中，`@testable` 就是一个修饰器，它修改了 `MyTestableClass` 这个类的行为，为它加上了静态属性 `isTestable`。  
基本上，修饰器的行为如下：

```javascript
@decorator
class A {}

// 等同于
class A {}
A = decorator(A) || A;
```

也就是说，修饰器本质上就是能在编译时执行的函数，且修饰器函数的行为其实就等同于函数式编程里面的高阶函数，有兴趣的小伙伴可以跳往另一篇文章 —— [ES6 - 高阶函数介绍](https://github.com/gu-xionghong/iCoding/blob/master/2016/函数式编程/ES6的高阶函数.md)。

### 类的修饰

上面的例子中， `testable` 函数的参数 `target` 就是所要修饰的对象，如果希望修饰器的行为能够根据目标对象的不同而不同，就要再封装一层函数，修饰器也能通过 `prototype` 属性给类添加方法

```javascript
function testable(isTestable) {
  return function(target) {
    target.isTestable = isTestable;
  };
}

@testable(true)
class MyTestableClass {}
MyTestableClass.isTestable; // true

@testable(false)
class MyTestableClass {}
MyTestableClass.isTestable; // false

function testPrototype(target) {
  target.prototype.isTestable = true;
}

@testPrototype
class MyTestPrototypeClass {}

let obj = new MyTestPrototypeClass();
obj.isTestable; // true
```

下面是另外一个例子

```javascript
// mixins.js
export function mixins(...list) {
  return function(target) {
    Objects.assign(target.prototype, ...list);
  };
}

// main.js
import { mixins } from './mixins';

const Foo = {
  foo() {
    console.log('foo');
  }
};

@mixins(Foo)
class Myclass {}

let obj = new MyClass();
obj.foo(); // 'foo'
```

上面的代码通过修饰器 `mixins` 可以为类添加指定的方法。  
另外，修饰器可以用 `Object.assign()` 模拟

```javascript
const Foo = {
  foo() {
    console.log('foo');
  }
};

class MyClass {}

Object.assign(MyClass.prototype, Foo);

let obj = new MyClass();
obj.foo(); // 'foo'
```

### 方法的修饰

修饰器不仅可以修饰类，还可以修饰类的属性。

```javascript
class Person {
  @readonly
  name() {
    return this.name;
  }
}
```

此时，修饰器函数一共可以接受 3 个参数，第 1 个参数是修饰的目标对象(`target`)，第 2 个参数是所要修饰的属性名(`name`)，第 3 个参数是该属性的描述对象(`descriptor`)。

```javascript
readonly(Person.prototype, 'name', descriptor);

function readonly(target, name, descriptor) {
  // descriptor 对象默认值如下
  // {
  //  value: specifiedFunction,
  //  enumerable: false,
  //  configurable: true,
  //  writable: true
  // };
  descriptor.writable = false;
  return descriptor;
}

Object.defineProperty(Person.prototype, 'name', descriptor);
```

上面的代码说明，修饰器 `readonly` 会修改属性的描述对象 `descriptor`，然后被修改的描述对象再用来定义属性，下面是另一个例子

```javascript
class Person {
  @nonenumerable
  get kidCount() {
    return this.children.length;
  }
}

function nonenumerable(target, name, descriptor) {
  descriptor.enumerable = false;
  return descriptor;
}
```

修饰器有注释的作用

```javascript
@testable
class Person {
  @readonly
  @nonenumerable
  name() {
    return this.name;
  }
}
```

从上面的代码中，我们可以看出， `Person` 类是可测试的，而 `name` 方法是只读和不可枚举的。  
除了注释，修饰器还能用于类型检查，所以，对于类而言，这项功能相当有用，从长期来看，它将是 `Javascript` 代码静态分析的重要工具。

### React 修饰器使用

如[ES6 - 高阶函数介绍](https://github.com/gu-xionghong/iCoding/blob/master/2016/函数式编程/ES6的高阶函数.md)里有介绍一些 `React HOC` 的使用方式，而针对修饰器，`React` 也能充分利用其优势进行代码的组织,下面我们将以一个 `Redux connect` 的使用作为例子代码  
不使用修饰器 decorator

```javascript
import React from 'react';
import * as actionCreators from './actionCreators';
import { bindActionCreators } from 'redux';
import { connect } from 'react-redux';

function mapStateToProps(state) {
  return { todos: state.todos };
}

function mapDispatchToProps(dispatch) {
  return { actions: bindActionCreators(actionCreators, dispatch) };
}

class MyApp extends React.Component {
  // ...define your main app here
}

export default connect(
  mapStateToProps,
  mapDispatchToProps
)(MyApp);
```

使用修饰器

```javascript
import React from 'react';
import * as actionCreators from './actionCreators';
import { bindActionCreators } from 'redux';
import { connect } from 'react-redux';

function mapStateToProps(state) {
  return { todos: state.todos };
}

function mapDispatchToProps(dispatch) {
  return { actions: bindActionCreators(actionCreators, dispatch) };
}

@connect(
  mapStateToProps,
  mapDispatchToProps
)
export default class MyApp extends React.Component {
  // ...define your main app here
}
```

### 结束语

修饰器的使用很大程度的提高了代码的复用性及清晰性，对方法的修饰，能让我们更方便的对属性的描述对象进行操作，提升我们的代码质量，但因为修饰器现在还属于 `ES7` 提案内容，如今如果想要使用的话必须通过 `Babel` 编码器进行代码编译，这也是其弊端之一了~
