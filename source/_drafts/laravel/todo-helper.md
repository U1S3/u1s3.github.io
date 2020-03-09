---
title: 助手函数『Helper』
date: 2018-12-22 10:43:39
categories: Laravel
tags: [助手函数, Helper]
---

{% fullimage /uploads/laravel/helper/helper.jpg %}

<!-- more -->

## 系统助手函数

### app()

获取一个容器的实例

```php
// 获取的是 config/app.php 中的 locale 选项
app()->getLocale()
```

### getenv()

获取到 `.env` 文件中指定的值

### config()

访问配置值

```php
// 文件名称.选项 
// 还可以指定默认值，如果配置选项不存在，则返回默认值
config('app.timezone');
```

设置配置值

```php
config(['app.timezone' => 'America/Chicago']);
```

### __()

本地化

```php
__('Login') # 输出"登录"
```

### public_path()

`public` 文件夹物理路径

## 自定义助手函数

### 路由名转CSS类

```php
function route_class()
{
    return str_replace('.', '-', Route::currentRouteName());
}
```













