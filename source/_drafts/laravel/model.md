---
title: Model『模型』
date: 2018-01-05 15:20:22
categories: Laravel
---

{% fullimage /uploads/laravel/model/model.jpg %}

<!-- more -->

## 创建模型

```bash
# -cfm 同时控制器文件、生成工厂文件和数据库迁移文件
$ php artisan make:model Models/User
```

## 模型属性

### 可更新字段

可以被更新的字段。

```php
protected $fillable = []; 
```

### 被隐藏的字段

一般是密码等敏感信息。

```php
protected $hidden = [];
```

### 自定义数据表名

```php
protected $table = 'table_names';
```

### 停止维护时间戳

```php
public $timestamps = false;
```

### 数据类型转换

将数据库字段值转换为常见的数据类型。

```php
$casts = [
    'email_verified_at' => 'boolean',
];
```

支持转换的数据类型有： `integer`，`real`，`float`，`double`，`string`，`boolean`，`object`，`array`，`collection`，`date`，`datetime` 和 `timestamp`。

### 转换为Carbon对象

```php
protected $dates = ['date'];
```

## 模型内获取数据

```php
$this->attributes['name'];
$this->email;
```

## Links

[ 不太能理解 fillable 的作用？](https://laravel-china.org/topics/15712)

