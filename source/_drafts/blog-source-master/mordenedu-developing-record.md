---
title: 学校报名系统开发记录
comments: false
date: 2018-10-19 15:39:46
keywords: laravel
description: 学校报名系统开发记录。
summary: 好记性不如烂笔头。
categories: laravel
tags: [note]
---

## 初始化项目



配置`Carbon`友好显示时间

```php
// app/Providers/AppServiceProvider.php
public function boot()
{
    \Carbon\Carbon::setLocale('zh');
}
```



安装`laravel-debugbar`

```bash
$ composer require barryvdh/laravel-debugbar --dev 
```



新增开发需要用的依赖包

```bash
$ yarn add normalize.css stylus stylus-loader browser-sync browser-sync-webpack-plugin --dev
```

配置`webpack.mix.js`文件

```js
mix.js('resources/assets/js/app.js', 'public/js')
    .stylus('resources/assets/stylus/app.styl', 'public/css')
    .browserSync('http://mordenedu.test/')
    .version();
```

运行`$ npm run watch-poll`，热加载页面



## 基础布局

编辑`resources`目录下的文件，移除不需要的文件、引用，创建项目主文件`app.blade.php`

```php
<!doctype html>
<html lang="{{ app()->getLocale() }}">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <!-- CSRF Token -->
    <meta name="csrf-token" content="{{ csrf_token() }}">
    <title>@yield('title') - {{ config('app.name') }}</title>
    <link rel="stylesheet" href="{{ mix('/css/app.css') }}">
</head>
<body>
    @yield('content')
    <script src="{{ mix('/js/app.js') }}"></script src>
</body>
</html>
```

创建页面控制器，定义首页方法`root()`，创建视图文件，定义路由

```bash
$ php artisan make:controller PageController
```

```php
// app/Http/Controller/PageController.php
public function root(){}
```

```php
// resources/views/page/root.blade.php
@extends('layouts.app')
@section('title', '首页')
@section('content')
    首页
@stop
```

```php
// routes/web.php
Route::get('/', 'PageController@root')->name('root');
```

## 用户认证

使用laravel提供的用户认证脚手架

```bash
$ php artisan make:auth
```

移除`routes/web.php`文件中的生成多余路由`home`

移除生成的多余文件`app/Http/Controllers/HomeController.php` 、`resources/views/home.blade.php`

调整迁移文件`database/migrations/create_users_table.php`，修改`name`为`unique`项

执行迁移命令

```bash
$ php artisan migrate
```

修改登陆/注册后跳转页面，由`/home`改为`/`

修改使用用户名登陆

```php
// app/Http/Controller/Auth/LoginController.php
public function username()
{
    return 'name';
}
```

登录页完成，代码具体查阅github仓库

用户退出表单

```php
// 通过js触发表单提交事件
<a id="logout-btn" href="javascript:void(0);">退出</a>
<form id="logout-form" class="hide" method="post" action="{{ route('logout') }}">
	{{ csrf_field() }}
</form>
```

##  个人中心

配置路由

```php
Route::resource('users', 'UserController', ['only' => ['show', 'update', 'edit']]);
```

生成控制器

```bash
$ php artisan make:controller UserControler
```

创建相应方法

`users`表新增头像字段，生成并配置迁移文件

```bash
$ php artisan make:migration add_avatar_to_users_table --table=users
```

创建表单请求，在需要的位置引用

```bash
$ php artisan make:request UserRequest
```

```php
public function update(UserRequest $request, User $user){}
```

创建提示信息文件`resources/views/layouts/_message.blade.php`

创建提示错误文件`resources/views/layouts/_error.blade.php`

创建图片上传工具类`app/Handlers/ImageUploadHandler.php`

安装图片处理库，生成配置文件

```bash
$ composer require intervention/image
$ php artisan vendor:publish --provider="Intervention\Image\ImageServiceProviderLaravel5" 
```

头像上传完成，代码具体查阅github仓库

生成授权策略文件，配置授权策略

```bash
$ php artisan make:policy UserPolicy
```

```php
// app/Policies/UserPolicy.php
public function update(User $currentUser, User $user)
{
    return $currentUser->id === $user->id;
}
```

注册授权策略

```php
// app/Providers/AuthServiceProvider.php
protected $policies = [
    \App\Models\User::class => \App\Policies\UserPolicy::class,
];
```

使用授权策略

```php
// app/Http/Controllers/UserController.php
public function update(UserRequest $request,ImageUploadHandler $uploader, User $user)
{
    $this->authorize('update', $user);
}
```

## 招生

生成各种下拉菜单数据，填充数据

```bash
$ php artisan make:model Models/ExaminationSchool -m
$ php artisan make:seed ExaminationSchoolSeeder
```

配置`Faker`中文数据

```php
// config/app.php
'faker_locale' => 'zh_CN',
```

生成控制器，模型，规定路由

```bash
$ php artisan make:model Student -m -c
```

```php
Route::resource('students', 'Student');
```

填充假用户数据，代码具体查阅github仓库

填充假学生数据

```bash
$ php artisan make:factory StudentFactory
```

单独填充数据

```bash
$ php artisan db:seed--class=UserTableSeeder
```

配置模型文件中的关联数据

学生列表页完成，代码具体查阅github仓库

用预加载解决N+1问题

```php
$students = Student::with('user', '...')->paginate(30);
```

安装 `Active` 依赖库

```bash
$ composer require "hieu-le/active:~3.5"
```

编写本地作用域`scope` 用于根据目前状态分类，并加如`with()`解决N+1问题

个人中心招生列表完成

招生功能

[时间选择插件](https://xdsoft.net/jqplugins/datetimepicker/)

修复`Call to a member function diffForHumans() on string`

```php
<?php
namespace App;
use Illuminate\Database\Eloquent\Model;
class User extends Model
{
    // 修改这里
    protected $dates = [
        'created_at',
        'updated_at',
        'deleted_at',
        'merged_at',
    ];
}
```

数据验证

观察器

--

**待做**

数据验证规则

授权失败跳转页面 404 403等

[链接](https://laravel-china.org/courses/laravel-intermediate-training-5.5/637/edit-personal-data)

--

## Tips

`.env`文件

```php
// 读取
getenv('APP_ENV');
```

`config`目录

```php
// 读取
config('app.timezone');
// 设置
config(['app.timezone' => 'America/Chicago']);
```

## 扩展功能

L02中未使用的功能

- [文字验证码](https://laravel-china.org/courses/laravel-intermediate-training-5.5/645/registration-verification-code)

- [极验验证码](https://packagist.org/packages/jormin/laravel-geetest)

