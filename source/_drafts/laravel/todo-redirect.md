---
title: 重定向 『Redirect』
date: 2019-09-26 11:03:47
categories: Laravel
tags: [重定向, Redirect]
---

{% fullimage /uploads/laravel/redirect/redirect.jpg  %}

<!-- more -->

## 默认跳转

默认会跳转到 `/home` ，可修改文件并加上友好提示消息

`app/Http/Middleware/RedirectIfAuthenticated.php`

```php
public function handle($request, Closure $next, $guard = null)
{
    if (Auth::guard($guard)->check()) {
        session()->flash('info', '您已登录，无需再次操作。');
        return redirect('/');
    }
    return $next($request);
}
```

## 跳转操作

### 返回前页

```php
return back();
return redirect()->back();
```

### 前往指定页

```php
return redirect('/');
```

### 301

```php
return redirect($topic->link(), 301);
```

## 传递数据

### withInput

```php
// 重定向到上一个页面并返回上一次输入的数据
return redirect()->back()->withInput();
```

## 传递路径

### intended

接收一个默认跳转地址参数，当上一次请求记录为空时，跳转到默认地址上。

```php
// 重定向到之前尝试访问的页面
$fallback = route('users.show', Auth::user());
return redirect()->intended($fallback);
```



























### 

```php
return redirect()->route('replies.index')->with('success', '删除成功！');
return redirect()->to($reply->topic->link())->with('success', '创建成功！');
```

