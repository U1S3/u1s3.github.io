---
title: 变量的解构赋值
date: 2019-11-10 12:03:37
categories: JavaScript
---

ES6 允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构（Destructuring）。 

如果等号的右边不是数组（或者严格地说，不是可遍历的结构），那么将会报错。 

## 解构赋值的规则

只要等号右边的值不是对象或数组，就先将其转为对象。但由于 `undefined` 和 `null` 无法转为对象，所以无法对它们进行解构赋值。

```javascript
let { prop: x } = undefined; // 报错
let { prop: y } = null; // 报错
```

## 数组解构

### 完全解构

```js
let [one, two, three] = [1, 2, 3];
// one = 1, two = 2, three = 3

let [one, [two, [three]]] = [1, [2, [3]]];
// one = 1, two = 2, three = 3
```

本质上，这种写法属于“模式匹配”，只要等号两边的模式相同，左边的变量就会被赋予对应的值。 

```javascript
let [one, , three] = [1, 2, 3];
// one = 1, three = 3
```

```javascript
let [one, ...two] = [1, 2, 3];
// one = 1, two = [2, 3];
```

```javascript
let [one, two, ...three] = [1];
// one = 1, two = undefined, three = []
```

 如果解构不成功，变量的值就等于`undefined`：

```javascript
let [one] = [];
// one = undefined

let [two, one] = [2];
// one = undefined
```

### 不完全解构

还有一种情况是不完全解构，等号左边的模式，只匹配一部分的等号右边的数组，解构依然可以成功。 

```javascript
let [one, two] = [1, 2, 3];
// one = 1, two = 2
```

```javascript
let [one, [two], three] = [1, [2, 4], 3];
// one = 1, two = 2, three = 3
```

### 默认值

解构赋值允许指定默认值 ，但 ES6 内部使用严格相等运算符（`===`），判断一个位置是否有值。所以，只有当一个数组成员严格等于`undefined`，默认值才会生效。

```javascript
let [one, two = 2] = [1];
// one = 1, two = 2
```

```javascript
let [one = '1'] = [null];
// one = null
```

如果默认值是表达式 ，那么这个表达式是惰性求值的，只有在用到的时候才求值。 

```javascript
// 因为 one 能取到值等于1，所以 f() 不会执行
function f() {
	console.log('first');
}
let [one = f()] = [1];
```

默认值可以引用解构赋值的其他变量，但该变量必须已经声明。 

```javascript
let [one, two = one] = [1];
// one = 1, two = 1
```

```javascript
let [one = two, two = 2] = [];
// Uncaught ReferenceError: Cannot access 'two' before initialization
```

## 对象解构

### 一般用法

```javascript
let {one, two} = { one: 1, two: 2 };
// one = 1, two = 2
```

对象解构和数组解构的区别在于数组解构的变量取值由它的位置决定，而对象解构变量必须与属性同名，才能取到正确的值。

对象的解构赋值，非常棒的一点是可以很方便地将现有对象的方法，赋值到某个变量。 

```javascript
// 将 console.log 赋值到 log
let { log } = console;

// 将 Math 的对数、正弦、余弦赋值到对应的变量上
let { log, sin, cos } = Math;
```

如果变量名与属性名不一致，需要改变写法：

```javascript
let { one: first } = { one: 1 };
// first = 1
```

上面这种写法说明了对象的解构赋值是下面形式的简写：

```javascript
let { one: one, two: two } = { one: 1, two: 2 };
```

解释了对象的解构赋值的内部机制，是先找到同名属性，然后再赋给对应的变量。真正被赋值的是后者，而不是前者。

```javascript
// one 是匹配模式 first 是变量
let { one: first } = { one: 1 };
// first = 1
// one: Uncaught ReferenceError: one is not defined
```

### 嵌套取值

与数组相同，对象解构也课用于嵌套：

```javascript
let obj = {
    one: [
        "A",
        { first: 1 }
    ]
};

// one是模式不会被赋值
let { one: [a, { first } } = obj;
// a = "A", first = 1

// 如果需要赋值需要单独解构
let { one, one: [a, { first }]} = obj;
// a = "A", first = 1, one = ["A", { first: 1 }]
```

嵌套赋值可以赋值给预先定义好的变量：

```javascript
let obj = {};
let arr = [];
{ one: obj.first, twp: arr[0] } = { one: 1, two: 2 }；
// obj = { first: 1 }, arr[0] = 2
```

### 继承属性

对象的解构赋值可以取到继承来的属性：

```javascript
const one = {};
const two = { second: '2' };
Object.setPrototypeOf(one, two);

const { second } = one;
// second = 2
```

### 默认值

```javascript
let { one, two = 2 } = {1};
// one = 1, two = 2

let { one: first = 1 } = {};
// first = 1

let {one: first = 1} = { one: 2 };
// first = 2
```

和数组解构相同，只有当一个对象成员严格等于`undefined`，默认值才会生效。

### 注意点

如果解构赋值使用了已经声明的变量，需要用括号括起来：

```javascript
// 这种会报错，因为 JavaScript 引擎会将 {x} 理解成一个代码块
let one;
{ one } = { one: 1 };

// 这种不会报错，只有不将大括号写在行首，才能解决这个问题
({ one } = { one: 1 });
```

解构赋值允许等号左侧的模式中不放置任何变量：

```javascript
// 这些毫无意义的写法是合法的
({} = [1, 2]);
({} = 'one');
({} = []);
```

因为数组的本质是对象，所以可以使用对象的解构方法来解构数组：

```javascript
let arr = [1, 2, 3];
let { 0: first, [arr.length -1]: last } = arr;
// first = 1, last = 3
```

## 字符串解构

字符串也可以使用解构赋值，使用时字符串被转换成类似数组的对象：

```javascript
const [a, b, c, d, e] = 'hello';
// a = 'h', b = 'e', c = 'l', d = 'l', e = 'o'
```

类似数组的对象都有一个 `length` 属性：

```javascript
let { length: len } = 'hello';
// len = 5
```

## 数值和布尔值解构

对数组和布尔值解构时会先转换成对象：

```javascript
let { toString: s } = 123;
s === Number.prototype.toString // true

let { toString: b } = true;
b === Boolean.prototype.toString // true
```

## 函数参数解构

对于函数参数，当他传入函数时，就已经被自动解构了：

```javascript
function sum([one, two]) {
    return one + two;
}
sum([1, 2]); // 3
```

```javascript
[[1, 2], [3, 4]].map((a, b) => a + b);
// [3, 7]
```

同样的，函数参数的解构也可以使用默认值：

```javascript
function sum({ first = 1, second = 2 } = {}) {
    return first + second;
}
sum(); // 3
sum({ first: 2 }); // 4
```

但是不能写成：

```javascript
// 这里不是为变量指定默认值，而是为函数指定默认值
function sum({ first, second } = { first: 1, second: 2 }) {
    return first + second;
}
```

`undefined` 会触发函数参数的默认值：

```javascript
[1, undefined, 3].map((four = 4) => four);
// [1, undefined, 3]
```

## 关于圆括号

> 只要有可能导致解构的歧义，就不得使用圆括号。

### 不能使用的情况

#### 变量声明

在变量声明语句中不能使用：

```javascript
// 以下使用全部报错
let [(one)] = [1];
let { one: (first) } = {};
let ({ one: first }) = {};
let { ( one: first ) } = {};
let { (one): first } = {};
let { one: ({ first: second }) } = { one: { first: 2 } };
```

#### 函数参数

函数参数也属于变量声明：

```javascript
// 以下使用全部报错
function fun([(one)]) { return one; }
function fun ([one, (two)]) { return two; }
```

#### 赋值语句

```javascript
// 把整个模式放到圆括号 报错
({ one: first }) = { one: 1 };

// 把一部分模式放到圆括号 报错
[( one: first ), { two: second }] = [{}, {}];
```

### 可以使用的情况

可以使用圆括号的情况其实只有一种，赋值语句的非模式部分：

```javascript
// 模式是取数组的第一个成员，和圆括号无关
[(one)] = [1];

// 模式时 one 不是 first
({ one: (first) } = {});

// 也是取数组的第一个成员
[(parseInt.prop)] = [1];
```

这里的三行都可以正确执行，因为第一他们是赋值语句而不是声明语句，第二圆括号都不属于模式的一部分。

## 解构复制的用途

### 交换变量的值

```javascript
let one = 1;
let two = 2;

[one, two] = [two, one];
// one = 2, two = 1
```

### 函数返回值

返回多个值：

```javascript
function fun() {
    return [1, 2, 3];
}
let [one, two, three] = fun();
// one = 1, two = 2, three = 3
```

返回一个对象：

```javascript
function fun() {
    return {
        one: 1,
        two: 2
    };
}
let { one, two } = fun();
// one = 1, two = 2
```

### 定义函数参数

```javascript
// 有序
function fun([one, two, three]) {}
fun([1, 2, 3]);

// 无序
function fun({two, three, one}) {}
f({ one: 1, three: 3, two: 2 });
```

### 函数参数默认值

```javascript
function fun({ 
    async = true, 
    beforeSend = function(){},
}) {}
```

### 提取 JSON 数据

```javascript
let jsonData = {
    id: 1,
    status: "OK",
    data: [867, 5309]
};

let {id, status, data: number} = jsonData;
// id = 1, status = "OK", data = [867, 5309]
```

### 遍历 Map 结构

任何部署了 Iterator 接口的对象，都可以用 `for...of` 循环遍历。

Map 结构原生支持 Iterator 接口，配合变量的解构赋值，可非常方便的获取键名和键值。

```javascript
const map = new Map();
map.set('first', 'hello');
map.set('second', 'world');

for (let [key, value] of map) {
    console.log(key + " is " + value);
}
// first is hello
// second is world
```

如果只需要键名或键值：

```javascript
// 键名
for (let [key] of map) {}

// 键值
for (let [, value] of map) {}
```

### 输入模块指定方法

```javascript
const { SourceMapConsumer, SourceNode } = require("source-map");
```

























