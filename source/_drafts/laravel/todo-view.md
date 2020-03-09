---
title: 视图『View』
date: 2018-12-31 14:51:14
categories: Laravel
tags: [视图, View]
---

{% fullimage /uploads/laravel/view/view.jpg %}

<!-- more -->

## 用户认证

```php
@guest
    // 未登录显示
@else
    // 已登录显示
@endguest
```









## 表单请求

### CSRF

```php
{{ csrf_field() }}
```

### 请求伪造

```php
{{ method_field('PUT') }}	// DELETE PATCH
```

### 最后输入

```php
{{ old('name', $user->name) }}
```















```php
<form action="" method="POST" accept-charset="UTF-8" enctype="multipart/form-data">


</form>
```















## 渲染视图

```php
...
public function action()
{
    return view('action');
}
...
```



## 模板继承

父模板

```php
<!DOCTYPE html>
<html lang="en">
<head>
  <title>@yield('title', 'default title')</title>
</head>
<body>
  @yield('content')
</body>
</html>
```

子模板

```php
@extends('layouts.default')
@section('title', 'page title')
@section('content')
  code...
@stop
```











## 基础模板

```html
<!doctype html>
<html lang="{{ app()->getLocale() }}">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="csrf-token" content="{{ csrf_token() }}">
    <title>@yield('title', 'Jaina')</title>
    <link rel="stylesheet" href="{{ mix('css/app.css') }}">
    @yield('css')
</head>
<body>
    <div id="app" class="{{ route_class() }}-page">
        @include('layouts._header')
        <div class="container">
            @yield('content')
        </div>
        @include('layouts._footer')
    </div>
    <script src="{{ mix('js/app.js') }}"></script>
    @yield('js')
</body>
</html>
```

## 语法规则

### 继承

```php
@extends('layouts.app')
```

### 引入

```php
@include('layouts._header', ['user' => $user])
```

#### 视条件引入

```php
@includeWhen($boolean, 'view.name', ['some' => 'data'])
//  当用户登录才引入
@includeWhen(Auth::check(), 'topics._reply_box', ['topic' => $topic])
```

### 占位符

```php
@yield('title', 'default title')
@section('title', 'page title') 
```

```php
@yield('content')
@section('content')
@stop
```

## 数据输出

### 路由

```php
{{ route('users.show', $topic->user_id) }}
{{ route('users.show', [$topic->user_id]) }}
```

### 源码输出

```php
// 会自动调用 PHP `htmlspecialchars` 函数来避免 `XSS` 攻击
{{ $topic->title }}
```

```php
// 需要做 `XSS` 安全处理
{!! $topic->body !!}
```



## 条件规则

### 授权策略

```php
@can('update', $topic)
@endcan
```

[Laravel 授权策略提供的 @can Blade 命令?](https://laravel-china.org/topics/8699/the-at-can-blade-command-provided-by-the-laravel-authorization-policy)

### 循环生命周期

```php
@foreach($users as $user)
    @if ($loop->first)
        第一次循环
    @endif
    @if ($loop->last)
        最后一次循环
    @endif
@endforeach
```

## 错误提示

```php
@if (count($errors) > 0)
    @foreach ($errors->all() as $error)
    	{{ $error }}
    @endforeach
@endif
```

## 视图共享

[v5.5文档](https://laravel-china.org/docs/laravel/5.5/views/1299#sharing-data-with-all-views)