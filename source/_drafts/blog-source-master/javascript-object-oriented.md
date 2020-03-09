---
title: JavaScript Object-Oriented
comments: true
date: 2019-01-03 00:03:45
summary: JavaScript面向对象的程序设计。
categories: javascript
---

# 理解对象

## 属性类型

ES5定义在定义只有内部采用的特性 `attribute` 时，描述了属性 `property` 的各种特征。

这些属性是为了实现JavaScript引擎使用，不能再JavaScript中直接访问。

### 数据属性

数据属性包含一个数据值的位置。在这个位置可以读取和写入。

#### [[Configurable]]

能否通过 `delete` 删除属性从而重新定义属性，能否修改属性的特性，能否把属性修改为访问器属性。直接在对象上定义属性时默认为 `true`。

#### [[Enumerable]]

能否通过 `for-in` 循环返回属性。直接在对象上定义属性时默认为 `true`。

#### [[Writable]]

能否修改属性的值。直接在对象上定义属性时默认为 `true`。

#### [[Value]]

包含这个属性的数据值。默认值为 `undefined`。

#### 修改

要修改数据属性只能使用 `Object.defineProperty()` 方法。

方法接收三个参数，属性所在的对象、属性的名字和一个描述符对象。

描述符对象必须是 `configurable`、 `enumerable`、 `writable`、 `value` 中的一个或多个。

```javascript
var person = {};
Object.defineProperty(person, 'name', {
  writable: false,
  value: 'Nicholas'
});
alert(person.name); // Nicholas
person.name = '池水太浅';
alert(person.name); // Nicholas
```

需要注意的是，当把 `configurable` 设置为 `false` 后是无法再修改为 `true` 的，且之后也只能修改 `writable` 特性。

另外，当使用上面这种调用 `Object.defineProperty()` 方法创建新属性时，如果不指定，其数据属性默认值都是 `false`。如果仅仅是修改已定义的属性的特性则无此限制。

一般情况下是用不上 `Object.defineProperty()` 提供的这些高级功能的。

### 访问器属性

访问器属性不包含数据值，包含一对不是必须的函数 `getter` 和 `setter`。

在读取访问器属性时，会调用 `getter`，这个函数负责返回有效的值。

在写入访问器属性时，会调用 `setter`，这个函数负责决定如何处理数据。

#### [[Configurable]]

能否通过 `delete` 删除属性从而重新定义属性，能否修改属性的特性，能否把属性修改为数据属性。直接在对象上定义属性时默认为 `true`。

#### [[Enumerable]]

能否通过 `for-in` 循环返回属性。直接在对象上定义属性时默认为 `true`。

#### [[Get]]

读书数据时调用的函数，默认值为 `undefined`。

#### [[Set]]

写入数据时调用的函数，默认值为 `undefined`。

#### 定义

访问器属性不能直接定义，必须使用 `Object.defineProperty()` 方法。

```javascript
var person = {
  _birth: 1990,
  age: 29
};
Object.defineProperty(person, 'birth', {
  get: function() {
    return this._birth;
  },
  set: function(newValue) {
    this._birth = newValue;
    this.age = new Date().getFullYear() - newValue;
  }
});
console.log(person.birth); // 1990
console.log(person.age); // 29
person.birth = 1991;
console.log(person.birth); // 1991
console.log(person.age); // 28
```

`_birth` 前面的下划线是一种常用记号，表示只能通过对象方法访问的属性。

上面这种设置一个属性的值会导致其他属性发生变化的方式是访问其属性的常见使用方法。

不一定非要同事制定 `getter` 和 `setter`，只其中一个意味着属性只读或只写。

## 定义多个属性

因为通过一次操作为对象定义多个属性的可能行很大，因此定义了可以通过描述符一次定义多个属性的方法。

`Object.defineProperties()` 方法接收两个对象参数，第一个是要添加和修改其属性的对象，第二个对象的属性与第一个对象中要添加或修改的属性一一对应。

```javascript
var person = {};
Object.defineProperties(person, {
  _birth: {
    writable: true,
    value: 1990
  },
  age: {
    writable: true,
    value: 29
  },
  birth: {
    get: function() {
      return this._birth;
    },
    set: function(newValue) {
      this._birth = newValue;
      this.age = new Date().getFullYear() - newValue;
    }
  }
});
console.log(person.birth); // 1990
console.log(person.age); // 29
person.birth = 1991;
console.log(person.birth); // 1991
console.log(person.age); // 28
```

## 读取属性的特性

`Object.getOwnPropertyDescriptor()` 方法接收两个参数，属性所在的对象和要读取其描述符的名称。

返回值是一个对象，根据其是数据属性或访问器属性返回相应的特性。

```javascript
var person = {};
Object.defineProperties(person, {
  _birth: {
    writable: true,
    value: 1990
  },
  age: {
    writable: true,
    value: 29
  },
  birth: {
    get: function() {
      return this._birth;
    },
    set: function(newValue) {
      this._birth = newValue;
      this.age = new Date().getFullYear() - newValue;
    }
  }
});

// 数据属性
var descriptor = Object.getOwnPropertyDescriptor(person, '_birth');
console.log(descriptor.value); // 1990
console.log(descriptor.configurable); // false
console.log(typeof descriptor.get); // undefined
// 访问器属性
var descriptor = Object.getOwnPropertyDescriptor(person, 'birth');
console.log(descriptor.value); // undefined
console.log(descriptor.configurable); // false
console.log(typeof descriptor.get); // function
```

# 创建对象