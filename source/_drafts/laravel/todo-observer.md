---
title: 模型观察器『Observer』
date: 2018-12-22 10:43:39
categories: Laravel
tags: [模型观察器, Observer]
---

{% fullimage /uploads/laravel/observer/observer.jpg  %}

<!-- more -->

## 生命周期

`creating` , `created` , `updating` , `updated` , `saving` , `saved` , `deleting` , `deleted` , `restoring` , `restored`

## 触发条件

当一个新模型被初次保存将会触发 `creating` 以及 `created` 事件。

如果一个模型已经存在于数据库且调用了 `save` 方法，将会触发 `updating` 和 `updated` 事件。

在这两种情况下都会触发 `saving` 和 `saved` 事件。

## 创建

v5.5需要手动创建，规则 `app/Observers/UserObserver.php`

## 注册

编辑 `app/Providers/AppServiceProvider.php` 文件

```php
use App\User;
use App\Observers\UserObserver;
...
public function boot()
{
    User::observe(UserObserver::class);
}
```

## Links
[Laravel 中的模型事件与 Observer](https://laravel-china.org/articles/6657/model-events-and-observer-in-laravel)

[具体哪些操作会触发观察者事件？](https://laravel-china.org/topics/18762)
