---
title: Auth『用户认证』
date: 2018-01-13 10:28:13
categories: Laravel
tags: [Auth, 用户认证]
---

{% fullimage /uploads/laravel/auth/auth.jpg  %}

<!-- more -->

## 脚手架

### 生成代码

```bash
$ php artisan ui:auth
```

此命令会生成路由 `Auth::routes();`，具体路由可到 `vendor/laravel/framework/src/Illuminate/Routing/Router.php` 中查看。

### 删除冗余

*routes/web.php* 

```php
Route::get('/home', 'HomeController@index')->name('home');
```

修改下列文件，替换 `/home` 为 `/`

`app/Http/Controllers/Auth/` 目录下的 `LoginController.php`、`RegisterController.php`、`VerificationController.php`、`ResetPasswordController.php`，以及 `app/Http/Middleware/RedirectIfAuthenticated.php`。

删除文件

```bash
$ rm -f app/Http/Controllers/HomeController.php resources/views/home.blade.php
```

### 执行迁移

```bash
$ php artisan migrate
```

## 邮箱验证

> 从 Laravel 5.7 起，Laravel 自带了邮箱验证的相关字段和功能，稍微调整即可。 

*app/Models/User.php* 

```php
class User extends Authenticatable implements MustVerifyEmail
```

*routes/web.php* 

```php
Auth::routes(['verify' => true]);

Route::group(['middleware' => ['auth', 'verified']], function() {
    // 需要验证邮箱后才能访问的路由都放置在这里
});
```





















































## 用户登陆

### 基础源码

```php
...
use Auth;
...
  if (Auth::attempt(['email' => $email, 'password' => $password], [remember])) {
    // 通过验证
  } else {
    // 未通过验证
  }
...
```

### 验证流程

- 使用 `email` 字段的值在数据库中查找

- 如果用户被找到

  1.先将传参的 `password` 值进行哈希加密，然后与数据库中 `password` 字段中已加密的密码进行匹配；

  2.如果匹配后两个值完全一致，会创建一个『会话』给通过认证的用户。会话在创建的同时，也会种下一个名为 `laravel_session` 的 HTTP Cookie，以此 Cookie 来记录用户登录状态，最终返回 `true`；

  3.如果匹配后两个值不一致，则返回 `false`；

- 如果用户未找到，则返回 `false`。

### 记住我

`Auth::attempt()` 接受两个参数，包括要进行用户身份认证的数组和是否记住登陆状态

```php
// 视图文件提交
<input type="checkbox" name="remember">
// 控制器处理
...
if (Auth::attempt(['email' => $email, 'password' => $password], $request->has('remember'))) {}
...
```

```php
// 例 : 
use Auth;
...
public function store(Request $request)
{
    $credentials = $this->validate($request, [
        'email' => 'required|email|max:255',
        'password' => 'required'
    ]);
    if (Auth::attempt($credentials, $request->has('remember')) {
        session()->flash('success', '欢迎回来！');
        return redirect()->route('users.show', [Auth::user()]);
    } else {
        session()->flash('danger', '很抱歉，您的邮箱和密码不匹配');
        return redirect()->back();
    }
}
```

## 常用方法

### 判断登陆状态

```php
Auth::check();
```

### 获取用户信息

```php
Auth::user();
```

### 注册成功后直接登陆

```php
Auth::login($user); // $user 是通过 User::create() 创建的对象
```

### 退出登陆

```php
Auth::logout();
```

## 身份验证

```php
public function __construct()
{
    $this->middleware('auth', [
        'except' => ['show', 'create', 'store']
        // 'only' => ['show', 'create', 'store']
    ]);
}
```

## 访客限制

```php
public function __construct()
{
    $this->middleware('guest', [
        'only' => ['create']
    ]);
}
```

