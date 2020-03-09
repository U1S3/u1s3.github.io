---
title: Laravel 向公共模板传值
date: 2019-11-30 14:14:14
categories: Laravel
---

在引用组建时，时常需要重复的传值。Laravel 提供了多种向公共模板传值的方法解决这个痛点。

*app/Providers/AppServiceProvider.php*

## 全局传值

```php
public function boot()
{
    ...
    view()->share('today', \Carbon\Carbon::today());
}
```

## 指定模板传值

```php
public function boot()
{
    ...
    view()->composer(['article.index'], function($view) {
        $view->with('today', \Carbon\Carbon::today());
    });
}
```

