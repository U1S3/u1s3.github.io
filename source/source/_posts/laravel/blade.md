---
title: Laravel Blade 模板语法
date: 2019-10-31 15:42:59
categories: Laravel
---

## 模板继承

### @endsection 和 @show

`@section()` 可以使用多种结尾，不同的结尾有不同的含义：

- `@endsection` 定义一个片段的结尾
- `@show` 定义的同时 **立即 yield** 这个片段

### 追加内容到父模板

在子模板中，可以使用 `@parent` 来为父模板追加内容，而不是覆盖父模板。

```php
@extends('layouts.app')
@section('sidebar')
    @parent
    <!-- 下面这个 p 会追加到父模板的 sidebar 中 -->
    <p>This is appended to the master sidebar.</p>
@endsection
```

<!-- more -->

## 渲染数据

### 不转义字符

Blade 双花括号 语句会自动使用 PHP 的 `htmlspecialchars` 函数传递来防范 XSS 攻击，如果不希望数据被转义，可以使用： 

```php
{!! $name !!}
```

### Array => JSON

在向 JavaScript 传递数据时有时候会传递一个数组，并将其渲染成 JSON 格式，但在 blade 模板中不应该使用 `json_encode`，Laravel 提供了 `@json` 指令来解决这一需求，其和 `json_encode` 具有相同的参数：

```php
<script>
    var app = @json($array);

    var app = @json($array, JSON_PRETTY_PRINT);
</script>
```

### 禁用双重编码

 默认情况下，Blade（和 Laravel 的 `e` 助手函数）将对 HTML 实体进行双重编码， 如果需要禁用：

*app/Providers/AppServiceProvider.php*

```php
use Illuminate\Support\Facades\Blade;
public function boot()
{
    Blade::withoutDoubleEncoding();
}
```

### blade 兼容 js 框架

许多 JavaScript 框架也使用 花括号 语法来表示预设的表达式应该显示在浏览器中，为了避免冲突，可以使用 `@` 符号通知 Blade 渲染引擎表达式应保持不变：

```php
Hello, @{{ name }}.
```

这里，`@` 会被 blade 自动删除，而值将交由 js 引擎渲染处理。

如果有整段的 js 变量，可以嵌套进 `@verbatim`，这样就不必每一个都加 `@`了：

```php
@verbatim
    <div class="container">
        Hello, {{ name }}.
    </div>
@endverbatim
```

## 控制结构

### 除非 @unless

```php
@unless (Auth::check())
    // 除非已登录不然就显示
    // 在这里本质上和 @guest 一个意思
@endunless
```

### 存在 @isset 

```php
@isset($records)
    // 找到记录时
@endisset
```

### 为空 @empty 

```php
@empty($records)
    // 记录为空时
@endempty
```

### 身份验证 @auth 和 @guest 

```php
@auth
    // 已登录
@endauth

@guest
    // 未登录
@endguest
```

在需要时，`@auth` 和 `@guest` 可以指定应被效验的身份验证

```php
@auth('admin')
    // 用户有管理权限
@endauth

@guest('admin')
    // 用户无管理权限
@endguest
```

### 片段指令 @hasSection

 检查片段是否包含指定内容：

```php
@hasSection('navigation')
    <div class="pull-right">
        @yield('navigation')
    </div>

    <div class="clearfix"></div>
@endif
```

### 循环 @forelse 

 ```php
@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>No users</p>
@endforelse
 ```

在循环中可以使用 `@break` 和 `@continue` 跳过当前迭代和结束循环，这两个指令也可以声明条件：

```php
@foreach ($users as $user)
    @continue($user->type == 1)

    <li>{{ $user->name }}</li>

    @break($user->number == 5)
@endforeach
```

### 循环变量 @loop 和 parent

循环过程中，在循环体内有一个可用的 `$loop` 变量。该变量提供了用于访问诸如当前循环的索引、当前是否为第一次或最后一次循环之类的少数有用的信息的途径： 

```php
@foreach ($users as $user)
    @if ($loop->first)
        This is the first iteration.
    @endif

    @if ($loop->last)
        This is the last iteration.
    @endif

    <p>This is user {{ $user->id }}</p>
@endforeach
```

在嵌套循环中，还可以借助 `parent` 属性访问父循环的 `$loop` 变量： 

```php
@foreach ($users as $user)
    @foreach ($user->posts as $post)
        @if ($loop->parent->first)
            This is first iteration of the parent loop.
        @endif
    @endforeach
@endforeach
```

| 属性               | 描述                             |
| ------------------ | -------------------------------- |
| `$loop->index`     | 当前迭代的索引 (从 0 开始计数)。 |
| `$loop->iteration` | 当前循环迭代 (从 1 开始计算)。   |
| `$loop->remaining` | 循环中剩余迭代的数量。           |
| `$loop->count`     | 被迭代的数组元素的总数。         |
| `$loop->first`     | 是否为循环的第一次迭代。         |
| `$loop->last`      | 是否为循环的最后一次迭代。       |
| `$loop->even`      | 是否是循环中的偶数迭代。         |
| `$loop->odd`       | 是否是循环中的奇数迭代。         |
| `$loop->depth`     | 当前迭代的嵌套深度级数。         |
| `$loop->parent`    | 嵌套循环中，父循环的循环变量。   |

## 表单

### CSRF 域 @csrf

所有 html 表单，都需要定义 CSRF令牌域。

```php
<form method="POST" action="/profile">
    @csrf
    ...
</form>
```

### Method 域 @method

html 表单不能发出 `put`, `patch`, `delete` 等请求，需要使用 `@method` 指令模仿动作。

```php
<form action="/foo/bar" method="POST">
    @method('PUT')
    ...
</form>
```

### 验证错误 @error

`@error` 指令可以快速检查验证错误消息是否存在，并且还可以使用 `@message` 输出验证错误。

```php
<input id="title" type="text" class="@error('title') is-invalid @enderror">
@error('title')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

`@error` 可以指定错误验证字段名作为第二个参数， 去获取验证错误内容并可以通过多种形式展示在页面上：

```php
<input id="email" type="email" class="@error('email', 'login') is-invalid @enderror">

@error('email', 'login')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

## 引入子视图

### 存在引入 @includeIf

使用 `@include` 时，如果传递了一个不存在的视图会报错，如果不确定视图是否存在，可以使用：

```php
@includeIf('view.name', ['some' => 'data'])
```

### 条件引入 @includeWhen

需要包含一个依赖于给定布尔条件的视图，可以使用 ：

```php
@includeWhen($boolean, 'view.name', ['some' => 'data'])
```

### 引入第一个存在 @includeFirst 

要引用给定视图数组中第一个存在的视图，可以使用： 

```php
@includeFirst(['custom.admin', 'admin'], ['some' => 'data'])
```

## 视图别名

假如有一个视图

*resources/views/includes/input.blade.php*

```php
<input type="{{ $type ?? 'text' }}">
```

注册别名

*app/Providers/AppServiceProvider.php*

```php
use Illuminate\Support\Facades\Blade;
public function boot()
{
    Blade::include('includes.input', 'input');
}
```

这样就可以像 blade 指令一样渲染视图

```php
@input(['type' => 'email'])
```

## 堆栈

Blade 允许将视图压入堆栈，这些视图能够在其它视图或布局中被渲染。

**这在子视图中指定需要的 JavaScript 库时非常有用**：

```php
@push('scripts')
    <script src="/example.js"></script>
@endpush
```

如果需要，可以多次压入堆栈。通过向 `@stack` 指令传递堆栈名称来完成堆栈内容的渲染： 

```php
<head>
    <!-- 头部内容 -->

    @stack('scripts')
</head>
```

 如果想要将内容预置在栈顶，需要使用 `@prepend` 指令： 

```php
@push('scripts')
    This will be second...
@endpush

// 然后...

@prepend('scripts')
    This will be first...
@endprepend
```

## 注释

blade 允许在视图中注释，且注释不会渲染到 html 中。

```php
{{-- comment --}}
```

## 原生 PHP

在某些情况下，需要直接在 blade 中书写 PHP 代码，这时候可以：

```php
@php
    //
@endphp
```

## 没整理

### 组件和插槽

### 为集合渲染视图

### Service 注入

### 扩展 Blade









