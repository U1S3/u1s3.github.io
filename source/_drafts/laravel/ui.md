---
title: Laravel UI
date: 2019-10-03 22:09:06
categories: Laravel
---

{% fullimage /uploads/laravel/ui/ui.jpg %}

<!-- more -->

> Laravel6之后，之前版本集成的bootstrap需要单独安装


## 扩展安装

```bash
$ composer require laravel/ui --dev
```

## 引入bootstrap

```bash
$ php artisan ui bootstrap
```

## 引入 vue

```bash
# 同时也引入了 bootstrap
$ php artisan ui vue
```

