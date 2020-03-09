---
title: Faker『模拟数据』
date: 2018-12-22 10:43:39
categories: Laravel
---

{% fullimage /uploads/laravel/faker/faker.jpg %}

<!-- more -->

## 创建实例

```php
$faker = app(Faker\Generator::class);
$faker = Faker\Factory::create();
```

## 本地化

*config/app.php*

```php
'faker_locale' => 'zh_CN'
```

## 用户信息

### 名称

```php
$faker->name;
```

### 邮箱

```php
$faker->safeEmail; 
```

### 邮编

```php
$faker->postcode;
```

### 电话

```php
$faker->phoneNumber;
```

## 随机文本

### 单词

```php
$faker->word;
```

### 小段

```php
$faker->sentence;
```

### 大段

```php
$faker->text();
```

### 链接

```php
$faker->url();
```

## 随机时间

### 日期

```php
$faker->date // "2011-02-10"
```

### 时间

```php
$faker->time // "13:03:55"
```

### 范围内取值

随机取一个月以内的时间

```php
$end = $faker->dateTimeThisMonth();

// $end 为生成时间的最大值 
// 这里的最大值指的是『后』而不是『前』，日期或时间越晚则越大
$start = $faker->dateTimeThisMonth($end);
```

## 随机数值

### 数值

```php
// 2 表示两位数
$faker->randomNumber(2);
```

### 范围内取值

```php
$faker->numberBetween(0, 5);
```

## 数组取值

### 数组

```php
$faker->randomElement($array);
```

## 数据特性

### 唯一性

```php
$faker->unique()->safeEmail;
```

