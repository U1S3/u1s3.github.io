---
title: 重新开始JS
comments: false
date: 2018-05-22 00:19:47
keywords: js
description: 重新开始JS。
summary: 温故而知新。
categories: js
tags: [廖雪峰]
---

## Tips

- 任何时候都使用严格模式 `use strict`  模式开发；
- 任何时候声明变量必须使用 `var` ；

## 字符串

### Tips

- 在ES6中，可以使用反引号 `` ` `` 表示多行字符；

- ES6新增了模板字符串，用于字符串拼接，模板字符串同样使用反引号 `` ` `` 表达；：

  ```javascript
  var name = "yigesan",
      age = 28;
  var profile = `my name is ${name}, and i'm ${age} years old`;
  ```

### 常用方法

- `string.toUpperCase()` 返回全部大写；
- `string.toLowerCase()` 返回全部小写；
- `string.indexOf(value[, fromindex])` 返回指定字符串出现的位置，没找到返回 -1；
- `string.substring(start[, stop])` 返回指定区间内的字符串，单一参数则获取到字符串结束；

## 数组

### Tips

- 任何时候不要直接修改数组的长度；

### 常用方法

- `array.indexOf(value)` 返回指定元素的索引，没找到返回 -1；
- `array.slice(start[, end])`  返回指定区间内的元素，单一参数则获取到数组结束，不传递参数则返回原数组；
- `array.push(item1, item2, item3)` 向数组末尾添加若干元素，返回数组新长度；
- `array.pop()` 删除数组的最后一个元素，返回被删除的元素；
- `array.unshift(item1, item2, item3)` 向数组头部添加若干元素，返回数组新长度；
- `array.shift()` 删除数组的第一个元素，返回被删除的元素；
- `array.sort([sortby])` 排序数组，默认按照字符编码顺序排序；
- `array.reverse()` 反转数组；
- `array.splice(start, howmany[, item1, item2, item3])` 从指定位置开始删除若干元素，然后再从该位置添加若干元素，返回被删除的元素，`howmany` 为 `0` 则不删除；
- `array.concat(array1, array2)` 连接数组并返回新数组；
- `array.join(separator)` 用指定分隔符连接数组元素，返回连接后的字符串；

## 对象

### Tips

- 任何时候对象的最后一个键值对都不要加逗号；

### 常用方法

- `delete Object.property` 删除指定的属性；
- `property in Object`  判断指定属性是否存在；
- `Object.hasOwnProperty(property)` 判断属性是否是对象自有的而非继承的；

## 条件判断

### Tips

- 任何时候都不要省略花括号；
- 任何时候都使用全等 `===` 进行相等比较；

## 循环

### Tips

- 可使用 `break` 跳出循环；

- `for ... in` 循环用于遍历对象属性或数组的值；

  ```javascript
  for (var key in Object) {
      console.log(key); // 可配合 hasOwnProperty 过滤掉继承来的属性
  }
  ```

## Map 和 Set

### Map

- 类似于php的关联数组；

- Map 拥有极快的速度；

- 初始化 Map 需要二位数组；

  ```javascript
  var m = new Map([[key1, value1], [key2, value2], [key3, value3]]);
  m.get(key1); // value1
  m.set(key4,value4);
  m.get(key4); // value4
  m.has(key5); // false
  m.delete(key2);
  ```

- 同一个 key 只能有一个 value，这一点和php的关联数组一个意思；

### Set

- key的集合，不存储value；

- key不能重复；

  ```javascript
  var s = new Set([key1, key2, key3]);
  s.add('key4');
  s.delete('key3');
  ```

## iterable

### Tips

- ES6新引入，用于遍历Array、Set、Map；

- iteralbel 使用  `for...of` 循环遍历数据；

  ```javascript
  var arr = [item1, item2, item3]; // Set和数组使用方法相同
  for (var x of arr) {
      console.log(x);
  }
  ```

  ```javascript
  var m = new Map([[key1, value1], [key2, value2], [key3, value3]]);
  for (var x of m) {
      console.log(x[0] + '=' + x[1]);
  }
  ```

- ES5.1引入了更好的 `array.forEach(element[, index, array])` 方法，它是iterable内置的；

  ```javascript
  var arr = [item1, item2, item3];
  arr.forEach(function(element, index, array) {
    console.log(index + '-' + element);
  });
  ```
  Set 和 Array 相似，但是因其没有索引，所以前两个参数都是元素本身
  ```javascript
  var m = new Map([[key1, value1], [key2, value2], [key3, value3]]);
  m.forEach(function(element, index, map) {
    console.log(index + '-' + element);
  });
  ```

## 函数

### Tips

- ES6引入了rest参数，可以使用 `function(argumentA,argumentB,...rest){}`， `...rest` 以数组的形式表示其他参数；

- 在函数内部使用 `var` 声明的变量的作用域是该函数整个函数体；

- 当函数嵌套时，内部函数可以访问外部函数定义的变量；

- js会把所有声明的变量提升到函数顶部，但并不会提升变量的赋值；

  ```javascript
  function foo() {
      var x = 'Hello, ' + y;
      console.log(x);
      var y = 'Bob';
  }
  // 实际上解析为
  function foo() {
      var y; // 提升变量y的申明，此时y为undefined
      var x = 'Hello, ' + y;
      console.log(x);
      y = 'Bob';
  }
  ```

  所以为了避免不必要的麻烦，要求在函数一开始就声明所有变量；