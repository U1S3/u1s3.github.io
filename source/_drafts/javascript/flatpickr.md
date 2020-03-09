---
title: 时间选择器『flatpickr』
date: 2018-01-10 22:24:56
categories: Laravel
tags: [时间选择器, flatpickr]
---



[flatpickr/flatpickr](https://github.com/flatpickr/flatpickr)

## 安装

```bash
$ yarn add flatpickr
```

## Laravel

`resources/js/bootstrap.js`

```js
// 引入时间选择器
window.flatpickr = require("flatpickr");
// 全局本土化
flatpickr.localize(require("flatpickr/dist/l10n/zh.js").default.zh);
// 设置星期一为第一天
flatpickr.l10ns.default.firstDayOfWeek = 1;
// 引入范围插件
window.rangePlugin = require("flatpickr/dist/plugins/rangePlugin.js");
```

`resources/sass/app.scss` 

```scss
// 引入时间选择器
@import '~flatpickr/dist/flatpickr.css';
```

`app.blade.php` 

```js
$("#from").flatpickr({
    // 使用范围插件
    "plugins": [new rangePlugin({ input: "#to"})],
    // 最大今天
    maxDate: "today"
});
```

