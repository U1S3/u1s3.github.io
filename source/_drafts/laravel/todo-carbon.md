---
title: 日期和时间『Carbon』
date: 2018-12-22 10:43:39
categories: Laravel
tags: [日期和时间, Carbon]
---

{% fullimage /uploads/laravel/carbon/carbon.jpg %}

<!-- more -->

[Carbon官方文档](https://carbon.nesbot.com/docs/)

## 格式化

### 人性化输出

```php
->diffForHumans(); // 1秒前
```

### 格式化

```php
->format('Y-m-d'); // 2019-01-01
```

## Getter

### 星期

```php
->dayName;	// 星期一
->shortDayName;	// 周一
->minDayName;	// 一
```

















## 本地化

`app/Providers/AppServiceProvider.php`

```php
public function boot()
{
    \Carbon\Carbon::setLocale('zh');
}
```


## 时间输出

```php
// 现在
Carbon:now();
```

## 格式化

### 人性化

```php
// 时间戳
Carbon::now()->timestamp;
// "2018-12-22 10:17:52"
Carbon:now()->toDateTimeString();
// 1秒前
Carbon:now()->diffForHumans();
```





 https://blog.csdn.net/lbwo001/article/details/53063867 



