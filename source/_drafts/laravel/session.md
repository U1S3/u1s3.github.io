---
title: Session『会话』
date: 2018-01-12 12:07:35
categories: Laravel
---

{% fullimage /uploads/laravel/session/session.jpg %}

<!-- more -->

## 闪存数据

> 闪存仅对下一次请求有效。

### 创建闪存

```php
session()->flash('key', 'value');
```

### 判断存在

```php
session()->has('key');
```

### 读取闪存

```php
session()->get('key');
```

使用时需先判断是否存在

```php
@if(session()->has('key'))
	{{ session()->get('key') }}
@endif
```



