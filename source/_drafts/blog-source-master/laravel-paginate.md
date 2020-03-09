---
title: Laravel Paginate
comments: false
date: 2018-12-22 10:43:39
keywords: laravel,paginate,分页
description: 分页处理。
summary: 分页处理。
categories: laravel
tags: [paginate,分页]
---

## 控制器

```php
$topics = Topic::paginate(30);
return view('template', compact('topics'));
```

## 视图

```php
{!! $topics->appends(Request::except('page'))->render() !!}
```

## Links

[分页的另一种写法](https://laravel-china.org/topics/8727/another-way-of-writing-pagination)

