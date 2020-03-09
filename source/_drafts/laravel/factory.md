---
title: Factory『模型工厂』
date: 2018-12-22 10:43:39
categories: Laravel
---

{% fullimage /uploads/laravel/factory/factory.jpg %}

<!-- more -->

## 创建工厂

```bash
$ php artisan make:factory TopicFacroty
```

## 工厂测试

进入 tinker 模式

```bash
$ php artisan tinker
```

创建一条数据

```tinker
>>> factory(App\Models\UserAddress::class)->make()
```

创建三条数据并插入数据库

```tinker
>>> factory(App\Models\UserAddress::class, 3)->create(['user_id' => 1])
```



