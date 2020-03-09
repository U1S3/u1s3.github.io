---
title: Laravel Mix『资源编译』
date: 2018-12-22 10:43:39
categories: Laravel
---

{% fullimage /uploads/laravel/mix/mix.jpg %}

<!-- more -->

## Mix 语法

### 文件编译

```php
.stylus('resources/assets/stylus/app.styl', 'public/css')
.js('resources/assets/js/app.js', 'public/js')
```

### 文件哈希

```php
.version()
```

在视图中使用 `mix()` 插入文件可动态更新哈希值

```php
<link rel="stylesheet" href="{{ mix('css/app.css')}}">
```

### 移动文件夹

```php
.copyDirectory('resources/assets/editor/js', 'public/js')
```

### 热刷新

```php
.browserSync('http://domain.test/')
```

## 启动编译

### 直接编译

```bash
$ npm run dev
```

### 监视文件

```bash
$ npm run watch-poll
```