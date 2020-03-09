---
title: ES6 回头看
date: 2019-11-10 14:03:35
categories: JavaScript
tags: [JavaScript, ES6]
---

阮一峰ES6文档，涉及到其他章节的内容，等看完全本再来看。

<!-- more -->

## 解构赋值

http://es6.ruanyifeng.com/#docs/destructuring 

对于 Set 结构，也可以使用数组的解构赋值。

```javascript
let [x, y, z] = new Set(['a', 'b', 'c']);
x // "a"
```

事实上，只要某种数据结构具有 Iterator 接口，都可以采用数组形式的解构赋值。

```javascript
function* fibs() {
  let a = 0;
  let b = 1;
  while (true) {
    yield a;
    [a, b] = [b, a + b];
  }
}

let [first, second, third, fourth, fifth, sixth] = fibs();
sixth // 5
```

上面代码中，`fibs`是一个 Generator 函数（参见《Generator 函数》一章），原生具有 Iterator 接口。解构赋值会依次从这个接口获取值。

## Node 加载

[http://es6.ruanyifeng.com/#docs/module-loader#Node-%E5%8A%A0%E8%BD%BD](http://es6.ruanyifeng.com/#docs/module-loader#Node-加载) 

