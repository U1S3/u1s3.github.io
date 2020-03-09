---
title: JavaScript的模块体系
date: 2019-11-5 09:30:02
categories: JavaScript
---

ES6 在语言标准的层面上实现了模块功能，取代了 AMD 和 CommonJs。

模块功能主要由两个命令构成：`export`和`import`。`export`命令用于规定模块的对外接口，`import`命令用于输入其他模块提供的功能。 

# 语法

## export

一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。

如果希望外部能够读取模块内部的某个变量，就必须使用`export`关键字输出该变量。 

```js
export var one = "one";
export var two = "two";
export function three(two, three) {
    return two + three;
}
```

```js
var one = "one";
var two = "two";
function three(two, three) {
    return two + three;
}

export { one, two, three }
```

上面两种方法是等价的，但是第二种写法可以放到文件末尾，可以清晰的看到输出的变量。

需要注意的是，`export` 可以放到模块的任何位置，但是必须处于模块顶层，不能放到块级作用域。

## import

`import` 可以加载通过 `export` 定义的对外接口。

```js
import { one, two, three } from "./example.js";
```

### 始终只读

`import` 加载的变量始终是**只读**的，不允许在加载模块的脚本里改写接口。

```js
import { one } from "./example.js";
one = {}; // 报错
```

但是：

```js
import { one } from "./example.js";
// 如果 one 是一个对象，改写其属性是允许的
one.abc = 'hello'; // 不报错
```

> 注意：在开发过程中，凡是加载的变量，都完全当做**只读**，不去修改它的值，才是正确的做法。

### 路径及后缀

加载的文件路径相对绝对都可`.js` 后缀可省略。

```js

```





## import 和 require

## 使用别名

`import` 和 `export` 都可以使用别名输出和加载。

```js
function one() {}
var two = "two";

// 通过别名输出时可以多次输出同一数据
export {
    one as first,
    two as second,
    two as dierge
}
```

```js
import { one as first }
```



## 自动严格模式

ES6 的模块会自动使用严格模式，无论文件头是否有 `"use strict";`





# 加载实现

## 浏览器环境

### 异步加载

```html
<script src="module.js"></script>
```

默认情况下，浏览器加载 JavaScript 脚本时是同步加载的，渲染引擎遇到 `<script>` 标签就会停下来，等到执行完脚本，再继续向下渲染。如果加载的是外部脚本，必须加入脚本下载的时间。如果脚本太大，浏览器会出现『卡死』的状态，为了避免这种不好的体验，浏览器环境下允许异步加载脚本：

```html
<script src="module.js" defer></script>
<script src="module.js" async></script>
```

`defer` 和 `async` 的效果都是异步加载脚本，浏览器遇到时会开始下载外部脚本，但不会等待它下载和执行，而是会直接执行后面的命令，但二者也是有区别的：

- `defer` 按顺序加载，渲染完页面再执行。
- `async` 无法保证顺序加载，下载完就执行。

### 加载 ES6 模块

```html
<script src="module.js" type="module"></script>
```

浏览器对于 `type="module"` 会自动使用异步加载，等同于使用了 `defer`，不会堵塞浏览器。

同时也可以手动使用 `async`，模块在加载完后立即执行，但会影响多个模块的顺讯。

使用外部模块时，有几点需要注意：

- 代码是在模块作用域之中运行，而不是在全局作用域运行。模块内部的顶层变量，外部不可见。
- 模块脚本自动采用严格模式，不管有没有声明 `use strict`。
- 模块之中，可以使用 `import` 命令加载其他模块（ `.js` 后缀不可省略，需要提供绝对或相对 URL ），也可使用 `export` 命令输出对外接口。
- 模块之中，顶层的 `this` 关键字返回 `undefined`，而不是指向 `window`。
- 同一个模块如果加载多次，将只执行一次。

利用顶层的 `this` 等于 `undefined` 这个语法点，可以侦测当前代码是否在 ES6 模块之中：

```javascript
const isNotModuleScript = this !== undefined;
```

## NODE 环境

### TODO

