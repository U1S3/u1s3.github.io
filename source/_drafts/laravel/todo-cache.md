---
title: 缓存系统『Cache』
date: 2018-12-22 10:43:39
categories: Laravel
tags: [缓存， Cache]
---

`.env`

```env
CACHE_DRIVER=redis
```

##  常用方法

### 存储

```php
Cache::put('key', data, 'expire');
```

### 取值

```php
// 取不到值执行闭包函数
Cache::remember('key', 'expire', function(){});
```

###清空

```php
Cache::forget('key');
```

## 自动缓存更新

### 新建监控器

` app/Observers/LinkObserver.php `

```php
<?php

namespace App\Observers;

use App\Models\Link;
use Cache;

class LinkObserver
{
    // 在保存时清空 cache_key 对应的缓存
    public function saved(Link $link)
    {
        Cache::forget($link->cache_key);
    }
}
```

### 注册监控器

`app/Providers/AppServiceProvider.php`

```php
<?php
.
.
.
class AppServiceProvider extends ServiceProvider
{
    .
    .
    .
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        \App\Models\User::observe(\App\Observers\UserObserver::class);
        \App\Models\Reply::observe(\App\Observers\ReplyObserver::class);
        \App\Models\Topic::observe(\App\Observers\TopicObserver::class);
        \App\Models\Link::observe(\App\Observers\LinkObserver::class);

    }
}
```













## 清除缓存

```bash
$ php artisan cache:clear
```

## 缓存配置

```bash
$ php artisan config:cache
```

## Links

[导航栏左边文章分类的自动缓存加载](https://laravel-china.org/topics/18494)

[关于教程里面的导航栏一些小优化](https://laravel-china.org/topics/17880)
