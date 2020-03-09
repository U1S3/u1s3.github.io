---
title: JavaScript Array
comments: true
date: 2018-12-31 14:51:14
summary: JavaScript中的数组类型。
categories: javascript
---

## 检测数组

```javascript
var colors = ['red', 'yellow', 'blue'];
console.log(Array.isArray(colors)); // true
```

对于一个网页或单一全局作用域而言， `colors instanceof Array` 可以返回满意的结果，但 `instanceof` 的问题在于其假定只有一个全局执行环境，一旦网页中包含多个框架，那实际上就存在多个不同的全局执行环境，也就存在多个不同的 `Array` 构造函数。

## 转换方法

转换方法实际上是调用的数组每一项的对应转换方法。

如果某一项是 `null`, `undefined`，则转换后为空字符串。

### valueOf()

```javascript
var colors = ['red', 'yellow', 'blue'];
console.log(colors.valueOf()); // ["red", "yellow", "blue"]
```

### toString()

```javascript
var colors = ['red', 'yellow', 'blue'];
console.log(colors.toString()); // red,yellow,blue
```

### toLocaleString()

```javascript
var colors = ['red', 'yellow', 'blue'];
console.log(colors.toLocaleString()); // red,yellow,blue
```

### 更换分隔符

默认情况下上述三种转换方法都是使用 `,` 多为分隔符，如果需要更换可以使用 `join` 来实现。

`join` 使用 `toSting()` 作为转换方法。

```javascript
var colors = ['red', 'yellow', 'blue'];
console.log(colors.join('|')); // red|yellow|blue
```

## 栈方法

栈方法的特点是后进先出 `LIFO(Last-In-Frist-Out)`。

### push()

向数组末端推入任意数量的项，返回修改后的数组长度。

```javascript
var colors = ['red', 'yellow', 'blue'];
var count = colors.push('orange', 'black');
console.log(count); // 5
console.log(colors); // ["red", "yellow", "blue", "orange", "black"]
```

### pop()

从数组最后一项，返回被移除的项。

```javascript
var colors = ['red', 'yellow', 'blue'];
var item = colors.pop();
console.log(item); // blue
console.log(colors); // ["red", "yellow"]
```

## 队列方法

队列方法的特点是先进先出 `FIFO(First-In-First-Out)`。

### shift()

移除数组第一个项，返回被移除的项。

```javascript
var colors = ['red', 'yellow', 'blue'];
var item = colors.shift();
console.log(item); // red   
console.log(colors); // ["yellow", "blue"]
```

结合 `push()` 和 `shift()` 可以像队列一样使用数组。

### unshift()

向数组前端添加任意数量的项，返回修改后的数组长度。

```javascript
var colors = ['red', 'yellow', 'blue'];
var count = colors.unshift('orange', 'black');
console.log(count); // 5
console.log(colors); // ["orange", "black", "red", "yellow", "blue"]
```

结合 `pop()` 和 `unshift()` 可以从反向来模拟队列。

## 重排序方法

### reverse()

反转数组

```javascript
var colors = ['red', 'yellow', 'blue'];
console.log(colors.reverse()); // ["blue", "yellow", "red"]
```

### sort()

默认情况下会先调用 `toString()` 方法，根据字符串数值大小升序排序。所以在大多数情况下不是最佳方案。

大多数情况下使用 `sort()` 的时候都会传递一个比较函数，自定义排序方案。

比较函数接收两个参数，比较过程中，如果 `value1` 应该在 `value2` 前面则返回负数，反之返回正数，相等返回0。

```javascript
// 升序
function compare(value1, value2) {
  if (value1 > value2) {
    return 1;
  } else if (value1 < value2) {
    return -1;
  } else {
    return 0;
  }
}
```

这个比较函数可以应对大多数的数据类型。

如果需要降序排序则交换比较函数的返回指即可。

```javascript
// 降序
function compare(value1, value2) {
  if (value1 > value2) {
    return -1;
  } else if (value1 < value2) {
    return 1;
  } else {
    return 0;
  }
}
```

但是如果仅仅是想要反转数组，使用 `reverse()` 会更快一些。

当要比较的是数值类型或其 `valueOf()` 的会返回数值类型，则可进一步简化比较函数。

```javascript
function compare(value1, value2) {
  return value2 - value1;
}
```

## 操作方法

### concat()

创建原数组的副本，如果传递了参数，将传递的参数逐次添加副本末端，返回新构建的数组。

```javascript
var colors = ['red', 'yellow', 'blue'];
var colors2 = colors.concat('orange', ['black', 'green']);
console.log(colors); // ["red", "yellow", "blue"]
console.log(colors2); // ["red", "yellow", "blue", "orange", "black", "green"]
```

### slice()

根据传值从原数组获取一个或多个项构建新数组。

如果只传了一个值，则从该参数指定位置开始获取项直到结束。

如果传了两个值，则第一个值表示开始位置，第二个值表示结束位置，但结果不包含结束位置的项。

如果结束位置小于起始位置则返回空数组。

```javascript
var colors = ["red", "yellow", "blue", "orange", "black", "green"];
var colors2 = colors.slice(2);
var colors3 = colors.slice(2, 4);
console.log(colors); // ["red", "yellow", "blue", "orange", "black", "green"]
console.log(colors2); // ["blue", "orange", "black", "green"]
console.log(colors3); // ["blue", "orange"]
```

如果传值中包含负数，则使用数组长度加上传值再获取项。

```javascript
var colors = ["red", "yellow", "blue", "orange", "black", "green"];
var colors2 = colors.slice(-3); // 等于 colors.slice(3)
var colors3 = colors.slice(-3, -1);  // 等于 colors.slice(3, 5)
console.log(colors); // ["red", "yellow", "blue", "orange", "black", "green"]
console.log(colors2); // ["orange", "black", "green"]
console.log(colors3); // ["orange", "black"]
```

### splice()

最强大的数组方法，始终返回由被删除的项构建的数组，如果没有删除项则返回空数组。

**删除**

进行删除操作时需要传递两个值，第一个值为起始位置，第二个值为删除数量。

```javascript
var colors = ["red", "yellow", "blue", "orange", "black", "green"];
var removed = colors.splice(3, 2);
console.log(colors); // ["red", "yellow", "blue", "green"];
console.log(removed); // ["orange", "black"];
```

**插入**

进行插入操作时需要传递三个以上的值，第一个值为起始位置，第二个值为0（删除数量），第三个及后面的值为要插入的项。

```javascript
var colors = ["red", "yellow", "blue"];
var removed = colors.splice(1, 0, 'orange', 'black', 'green');
console.log(colors); // ["red", "orange", "black", "green", "yellow", "blue"]
console.log(removed); // []
```

**替换**

进行替换操作和插入操作相似，但是第二项不能为0。插入的项数和删除的项数不必相等。

```javascript
var colors = ["red", "yellow", "blue"];
var removed = colors.splice(1, 1, 'orange', 'black', 'green');
console.log(colors); // ["red", "orange", "black", "green", "blue"]
console.log(removed); // ["yellow"]
```

## 位置方法

在数组中查找指定项出现的位置，在查找对比过程中使用 `===`。

接收两个参数，第一个是要查找的项，第二个是起点位置（默认0，可选），找到返回位置，没找到返回
 `-1`。

### indexOf()

默认从数组起始开始向后查找

```javascript
var colors = ["red", "yellow", "blue", "orange", "black", "green"]
console.log(colors.indexOf('orange')); // 3
console.log(colors.indexOf('orange', 2));  // 3
```

### lastIndexOf()

默认从数组结束开始向前查找

```javascript
var colors = ["red", "yellow", "blue", "orange", "black", "green"]
console.log(colors.lastIndexOf('orange')); // 3
console.log(colors.lastIndexOf('orange', 2)); // -1 从 blue开始往前找就找不到orange了
console.log(colors.lastIndexOf('orange', 3)); // 3 从 orange开始往前找可以找到
```

## 迭代方法

迭代方法接收两个参数，第一个是要在每一项上运行的函数，第二个是运行该函数的作用域对象（可选），会影响 `this` 的值。

传入的函数接收三个参数，项的值、项的位置和数组本身。

**迭代方法不会修改原数组的值。**

### every()

对每一项运行给定函数，全部返回 `true` 则返回 `true` 。

```javascript
var numbers = [1,2,3,4,5,4,3,2,1]
var result = numbers.every(function(item, index, array) {
  return (item > 2);
});
console.log(numbers); // [1, 2, 3, 4, 5, 4, 3, 2, 1]
console.log(result); // false
```

### some()

对每一项运行给定函数，如果有一项返回 `true` 则返回 `true` 。

```javascript
var numbers = [1,2,3,4,5,4,3,2,1]
var result = numbers.some(function(item, index, array) {
  return (item > 2);
});
console.log(numbers); // [1, 2, 3, 4, 5, 4, 3, 2, 1]
console.log(result); // true
```

### filter()

对每一项运行给定函数，返回该函数会返回 `true` 的项组成的数组。

```javascript
var numbers = [1,2,3,4,5,4,3,2,1]
var result = numbers.filter(function(item, index, array) {
  return (item > 2);
});
console.log(numbers); // [1, 2, 3, 4, 5, 4, 3, 2, 1]
console.log(result); // [3, 4, 5, 4, 3]
```

### map()

对每一项运行给定函数，返回每次函数调用结果组成的数组。

```javascript
var numbers = [1,2,3,4,5,4,3,2,1]
var result = numbers.map(function(item, index, array) {
  return (item * 2);
});
console.log(numbers); // [1, 2, 3, 4, 5, 4, 3, 2, 1]
console.log(result); // [2, 4, 6, 8, 10, 8, 6, 4, 2]
```

### forEach()

没有返回值，本质上和 `for` 一样。

```javascript
var numbers = [1,2,3,4,5,4,3,2,1]
var result = numbers.forEach(function(item, index, array) {
  return (item * 2);
});
console.log(numbers); // [1, 2, 3, 4, 5, 4, 3, 2, 1]
console.log(result); // undefined
```

## 归并方法

迭代数组所有项，构建一个最终返回值。

接收两个参数，第一个是要在每一项上运行的函数，第二个是作为归并基础的初始值。

传入的函数接收四个参数，前一个值，当前值、项的位置和数组本身。

函数返回的任何值都会作为第一个参数自动传给下一项。

第一次迭代发生在数组的第二项，因此第一个参数是数组的第一项，第二个参数是数组的第二项。

### reduce()

从前往后遍历。

```javascript
var numbers = [1,2,3,4,5,4,3,2,1]
var result = numbers.reduce(function(prev, next, index, array) {
  return (prev + next);
});
console.log(result); // 25
```

### reduceRight()

从后往前遍历。

```javascript
var numbers = [1,2,3,4,5,4,3,2,1]
var result = numbers.reduceRight(function(prev, next, index, array) {
  return (prev + next);
});
console.log(result); // 25
```

两种归并方法除了方向不同以外完全相同。
