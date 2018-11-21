---
title: React - 组件类型
date: 2017-08-30 08:28:10
toc: true
categories:
  - 知识补全
tags:
  - React
  - JavaScript
---

---

> [前端早读课 - 《【第 1042 期】React 之组件类型》](http://mp.weixin.qq.com/s/U4REXWqmVa-XgyR1_fUf7A) 读后总结

---

本文介绍了 `React` 当中有关组件的一系列概念:

- 元素与组件 `Element` & `Component`
- 函数定义与类定义组件 `Functional` & `Class`
- 展示与容器组件 `Presentational` & `Container`
- 有状态与无状态组件 `Stateful` & `Stateless`
- 受控与非受控组件 `Controlled` & `Uncontrolled`
- 组合与继承 `Composition` & `Inheritance`

## 元素与组件 Element & Component

使用 `html element` 作为标签项的 `React DOM`，被称为 _元素_，元素是构建 React 应用的最小单位。

```javascript
const element = <h1>Hello, world</h1>;
// 用JSX描述就相当于是调用React的方法创建了一个对象
const element = React.createElement('h1', null, 'Hello, world');
```

React 官方对组件的定义，是指在 UI 界面中，可以被独立划分的、可复用的、独立的模块。其实就类似于 JS 当中对 `function` 函数的定义，它一般会接收一个名为 `props` 的输入，然后返回相应的 `React` 元素，再交给 `ReactDOM`，最后渲染到屏幕上。

## 函数定义与类定义组件 Functional & Class

函数定义组件：

```javascript
function Title(props) {
  return <h1>Hello, {props.name}</h1>;
}

const Title = props => <h1>Hello, {props.name}</h1>;
```

类定义组件：

```javascript
class Title extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

## 展示与容器组件 Presentational & Container

_展示组件：_

- 主要负责组件内容如何展示
- 从 `props` 接收父组件传递来的数据
- 大多数情况可以通过函数定义组件声明

_容器组件：_

- 主要关注组件数据如何交互
- 拥有自身的 `state`，从服务器获取数据，或与 `redux` 等其他数据处理模块协作
- 需要通过类定义组件声明，并包含生命周期函数和其他附加方法

那么这样写具体有什么好处呢？

- 解耦了界面和数据的逻辑
- 更好的可复用性，比如同一个回复列表展示组件可以套用不同数据源的容器组件
- 利于团队协作，一个人负责界面结构，一个人负责数据交互

## 有状态与无状态组件 Stateful & Stateless

有状态组件是指这个组件能够获取储存改变应用或组件本身的状态数据，在 `React` 当中也就是 `state`，一些比较明显的特征是我们可以在这样的组件当中看到对 `this.state` 的初始化，或 this.setState 方法的调用等等。而无状态组件一般只能看到对 `this.props` 的调用，大部分无状态组件都是函数定义组件

## 受控与非受控组件 Controlled & Uncontrolled

### 受控组件

一般涉及到表单元素时我们才会使用这种分类方法。受控组件的值由 `props` 或 `state` 传入，用户在元素上交互或输入内容会引起应用 `state` 的改变。在 `state` 改变之后重新渲染组件，我们才能在页面中看到元素中值的变化，假如组件没有绑定事件处理函数改变 `state`，用户的输入是不会起到任何效果的，这也就是 _受控_ 的含义所在。

### 非受控组件

类似于传统的 `DOM` 表单控件，用户输入不会直接引起应用 `state` 的变化，我们也不会直接为非受控组件传入值。想要获取非受控组件，我们需要使用一个特殊的 `ref` 属性，同样也可以使用 `defaultValue` 属性来为其指定一次性的默认值。

## 组合与继承 Composition & Inheritance

即 `hoc`、`类继承` 的方式
