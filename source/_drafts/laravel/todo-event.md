---
title: 事件系统『Event』
date: 2019-10-05 14:08:39
categories: Laravel
tags: [事件系统, Event]
---

{% fullimage /uploads/laravel/event/event.jpg %}

<!-- more -->

## 事件监听器

> 单个事件可以拥有多个互不依赖的监听器

### 注册监听器

`app/Providers/EventServiceProvider.php`

```php
protected $listen = [
	...
    \Illuminate\Auth\Events\Verified::class => [
        // 单个事件可对应多个监听器
        \App\Listeners\EmailVerified::class,
    ],
];
```

### 生成监听器

监听器需要先注册再生成

```bash
$ php artisan event:generate
```

`app/Listeners/EmailVerified.php`

```php
public function handle(Verified $event)
{
    session()->flash('success', '邮箱验证成功 ^_^');
}
```

