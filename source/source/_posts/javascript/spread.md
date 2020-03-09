---
title: 扩展运算符
date: 2019-11-18 21:59:38
categories: JavaScript
---

扩展运算符（spread）是三个点（`...`）。它好比 rest 参数的逆运算，将一个数组转为用逗号分隔的参数序列。 

```javascript
console.log(...[1, 2, 3]); // 1 2 3
```

扩展运算符后面还可以跟其他数据：

```javascript
console.log(1, ...[2, 3, 4], 5); // 1 2 3 4 5
```

甚至是表达式：

```javascript
const arr = [
    ...(x > 0 ? ['a'] : []),
    'b'
];
```

如果扩展运算符后面跟的是空数组则无效果：

```javascript
[...[], 1] // [1]
```

## 函数调用

函数调用是扩展运算符最主要的用途之一：

```javascript
function push(array, ...items) {
    array.push(...items);
}
```

```javascript
function sum(x, y) {
    return x + y;
}
const numbers = [1, 2];
sum(...numbers); // 3
```

需要注意的是，只有在函数调用时扩展运算符才可以放到圆括号中。

## 替代函数 apply 方法

由于扩展运算符可以展开数组，所以不再需要 `apply` 方法，将数组转为函数的参数了：

```javascript
function f(x, y, z) {}

// ES5
f.apply(null, [1, 2, 3]);

// ES6
f(...[1, 2, 3]);
```

```javascript
// ES5
// 由于 JavaScript 不提供求数组最大元素的函数，所以只能套用 Math.max 函数，将数组转为一个参数序列，然后求最大值。
Math.max.apply(null, [1, 2, 3]);

// ES6
// 有了扩展运算符以后，就可以直接用 Math.max 了。
Math.max(...[1, 2, 3]);

// 等同于
Math.max(1, 2, 3);
```

```javascript
// ES5 
// push 方法的参数不能是数组，所以只好通过 apply 方法变通使用 push 方法。
var arr1 = [1, 2, 3];
var arr2 = [4, 5, 6];
Array.prototype.push.apply(arr1, arr2);

// ES6
// 有了扩展运算符，就可以直接将数组传入 push 方法。
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];
arr1.push(...arr2);
```

```javascript
// ES5
new (Date.bind.apply(Date, [null, 2019, 11, 18]));

// ES6
new Date(...[2019, 11, 18]);
```

## 复制数组

因为数组是复合的数据类型，通过赋值复制的话，只是复制了指向底层数据结构的指针，而不是克隆一个全新的数组。 

```javascript
const arr1 = [1, 2];
const arr2 = arr1;

arr2[0] = 2;
console.log(arr1); // [2, 2]
```

在 ES5 中，只能通过变通的方法复制数组：

```javascript
const arr1 = [1, 2];
const arr2 = arr1.concat();

arr2[0] = 2;
console.log(arr1); // [1, 2]
```

扩展运算符提供了复制数组的简便方法：

```javascript
const arr1 = [1, 2];
// 下面两种写法等价
const arr2 = [...arr1];
const [...arr2] = arr1;
```

## 合并数组

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const arr3 = [5, 6];

// ES5
arr1.concat(arr2, arr3);

// ES6
[...arr1, ...arr2, ...arr3]
```

 不过，这两种方法都是浅拷贝，使用的时候需要注意。 

```javascript
const arr1 = [{ foo: 1 }];
const arr2 = [{ bar: 2 }];

const arr3 = arr1.concat(arr2);
const arr4 = [...arr1, ...arr2];

arr3[0] === arr1[0] // true
arr4[0] === arr1[0] // true
```

上面代码中，`arr3` 和 `arr4` 是用两种不同方法合并而成的新数组，但是它们的成员都是对原数组成员的引用，这就是浅拷贝。如果修改了原数组的成员，会同步反映到新数组。 

## 转换数组

### 与解构赋值结合

```javascript
const [first, ...rest] = [1, 2, 3, 4, 5];
// first = 1, rest = [2, 3, 4, 5]

const [first, ...rest] = [];
// first = undefined, rest = []
```

如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。 

### 字符串

```javascript
[...'hello']
// [ "h", "e", "l", "l", "o" ]
```

凡是涉及到操作四个字节的 Unicode 字符的函数，最好都用扩展运算符改写。

### 对象

任何定义了遍历器（Iterator）接口的对象，都可以用扩展运算符转为真正的数组。

```javascript
let nodeList = document.querySelectorAll('div'); // 类似数组
let array = [...nodeList]; // 真数组
```

### Map、Set、Generator

扩展运算符内部调用的是数据结构的 Iterator 接口，因此只要具有 Iterator 接口的对象，都可以使用扩展运算符转为真正的数组。

```javascript
let map = new Map([
    [1, 'one'],
    [2, 'two'],
    [3, 'three'],
]);
[...map.keys()]; // [1, 2, 3]
```

```javascript
const go = function*(){
    yield 1;
    yield 2;
    yield 3;
}
[...go()] // [1, 2, 3]
```

