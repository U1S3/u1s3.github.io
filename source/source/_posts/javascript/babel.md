---
title: ES6转码器『Babel』
date: 2019-11-4 23:12:13
categories: JavaScript
---

> 大龄程序员的攀爬之旅从这里开始。

## 安装扩展

```bash
$ yarn add @babel/core -D
```

## 配置文件

*.babelrc*

```babelrc
{
  "presets": [
    "@babel/preset-env"
  ],
  "plugins": []
}
```

`presets` 设定转码规则，官方提供：

```bash
# 最新转码规则
$ yarn add @babel/preset-env -D
# react 转码规则
$ yarn add @babel/preset-react -D
```

<!-- more -->

## 命令行转码

安装扩展

```bash
$ yarn add @babel/cli -D
```

基本用法

```bash
# 转码结果输出到标准输出
$ npx babel example.js

# 写入到文件
$ npx babel example.js -o compiled.js # -o == --out-file

# 目录转码
$ npx babel src -d lib # -d == --out-dir

# 生成 source map
$ npx babel src -d lib -s
```

## 转码 API

Babel 默认只转换新的 JavaScript 句法（syntax），而不转换新的 API， 比如`Iterator`、`Generator`、`Set`、`Map`、`Proxy`、`Reflect`、`Symbol`、`Promise`等全局对象，以及一些定义在全局对象上的方法（比如`Object.assign`）都不会转码。 

如果需要转码 API， 必须使用`babel-polyfill`，为当前环境提供一个垫片。

```bash
$ yarn add @babel/polyfill -D
```

```js
require('@babel/polyfill');
```

## Babel API

在某些代码需要调用 Babel 的 [API](https://babeljs.io/docs/en/babel-core) 进行转码时，需要使用 `@babel/core` 模块。

```js
const babel = require('@babel/core');

// 字符串转码
babel.transform('code();', options);
// => { code, map, ast }

// 文件转码（异步）
babel.transformFile('filename.js', options, function(err, result) {
  result; // => { code, map, ast }
});

// 文件转码（同步）
babel.transformFileSync('filename.js', options);
// => { code, map, ast }

// Babel AST转码
babel.transformFromAst(ast, code, options);
// => { code, map, ast }
```

 配置对象`options`，参看[官方文档](http://babeljs.io/docs/usage/options/)。

```js
// option 例子
var es6Code = 'let x = n => n + 1';
var es5Code = require('@babel/core')
  .transform(es6Code, {
    presets: ['@babel/env']
  })
  .code;

console.log(es5Code);
// '"use strict";\n\nvar x = function x(n) {\n  return n + 1;\n};'
```

上面代码中，`transform`方法的第一个参数是一个字符串，表示需要被转换的 ES6 代码，第二个参数是转换的配置对象。 

## REPL

```bash
$ yarn add @babel/node -D
```

进入环境

```bash
$ npx babel-node
```

可直接运行 ES6 脚本

```bash
$ npx babel-node example.js
```

## 引用自动转码

> @babel/register 只对 require 命令加载的文件转码，且是实施转码，所以只适合开发环境使用。

```bash
$ yarn add @babel/register -D
```

*index.js*

```js
// 先加载扩展
require('@babel/register');
// 之后使用 require 加载的 .js|.jsx|.es|.es6 会自行转码
require('./example.js')
```

```bash
$ node index.js
```

