---
title: ES6-Arguments与Parameters用法总结
date: 2017-05-28 20:58:38
categories:
- JavaScript
tags:
- JavaScript
---

-------

> ES6 Arguments与Parameters用法总结

-------

### 扩展操作符
扩展操作符能将数组展开成独立的数值传给函数
```javascript
const myArray = [1, 2, 3];
Math.max(...myArray); //  === Math.max(1,2,3);   ouput：3
```

### Rest参数
`rest` 参数和扩展操作符拥有相同的语法，不同的是，`rest` 参数是把所有的参数收集起来转换成数组，而扩展操作符是把数组扩展成单独的参数。`rest` 参数在创建一个可变函数（即一个参数个数可变的函数）时尤其有用，`rest` 参数有着数组固有的优势，可以快捷地替换 `arguments` 对象，但是 `rest` 参数必须为最后一个参数，否则会导致报错。
```javascript
function testable(a, ...params, b) {
  console.log(a, ...params, b);
}

testable(1, 2, 3); // StntaxError: parameter

function testable(a, ...params {
  console.log(a, ...params);
}

testable(1, 2, 3); // 1 2 3
```

### 默认参数
```javascript
function myFunction(a = 1, b = [2]){
  console.log(a, b);
}

myFunction(5); // 5 [2]

// 在函数声明中做运算
function myFunction(a, b = ++a, c = a * b) {
  console.log(c);
}

myFunction(5); // 36

// 在函数声明中做参数判断
function myFunction(a = foo()) {
  console.log(a);
}

function foo() {
  return 'Missing parameter';
}

myFunction(); // 'Missing parameter'
```

### 解构赋值
```javascript
// 交换变量的值
[x, y] = [y, x];

// 从函数返回多个值
function example() {
  return [1, 2, 3];
};
const [a, b, c] = example();

// 返回一个对象
function example() {
  return {
    foo: 1,
    bar: 2
  }
};
const {foo, bar} = example();

// 函数参数的定义
// 有序参数
function f([x, y, z]) {...}
f([1, 2, 3]);

// 无序参数
function f({x, y, z}) {...}
f({z: 3, y: 2, x: 1});

// 提取 JSON 数据
let jsonData = {
  a: 1,
  b: 2
};

let {a, b} = jsonData;
```
