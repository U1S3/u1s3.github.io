---
title: 模板字符串
date: 2019-11-12 22:34:46
categories: JavaScript
---

JavaScript 在输出模板时，传统方法是通过不断的 `+` 来拼接，这种写法太过繁琐，ES6 引入了模板字符串解决这个痛点。

```javascript
let template = `
  My name is ${name},
  I am ${age} years old
`;
```

模板字符串使用反引号标识，可以当做普通字符串使用，也可以用来定义多行字符串，还能在字符串中嵌入变量。

## 多行字符串

如果使用模板字符串定义多行字符串，那么所有的空格和缩进都会被保留到输出中，但是开始和结束可以使用 `trim` 来消除：

```javascript
let template = `
  <ul>
    <li>1</li>
  </ul>
`.trim();
```

## 插入变量

说是插入变量，其实表达式和调用函数也是可以的：

```javascript
`My name is ${name}`;

`I am ${thisYear - bornYear} years old`;

`This function return ${fun()}`;
```

如果大括号中的不是字符串，一般会用 `toString` 方法转为字符串。

如果大括号内的变量没有声明，则会报错。

## 嵌套

模板字符串是支持嵌套使用的。

## 标签模板

模板字符串的功能，不仅仅是上面这些。它可以紧跟在一个函数名后面，该函数将被调用来处理这个模板字符串。这被称为“[标签模板](https://es6.ruanyifeng.com/#docs/string#%E6%A0%87%E7%AD%BE%E6%A8%A1%E6%9D%BF)”功能（tagged template）。 

