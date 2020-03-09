---
title: Tinker『REPL编程环境』
date: 2018-01-10 22:24:46
categories: Laravel
---

{% fullimage /uploads/laravel/tinker/tinker.jpg %}

<!-- more -->

> `Tinker` 是一个 `REPL` 编程环境

## 进入环境

```bash
$ php artisan tinker
```

## 使用方法

```bash
>>> use App\Models\User
>>> User::create(['name'=> 'fengling', 'email'=>'fengling@example.com','password'=>bcrypt('password')])
```

