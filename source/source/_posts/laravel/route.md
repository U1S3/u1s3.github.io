---
title: Laravel 路由设计『Route』
date: 2018-01-04 10:43:47
categories: Laravel
---

## 查看列表

```bash
$ php artisan route:list
```

## 生成URL

### URL

```php
route('users.index'); // http://peach.test/users
route('users.show', ['id' => 1]); // http://peach.test/users?id=1
```

### 重定向

```php
return redirect()->route('users.index');
```

<!-- more -->

## 定义路由

### 重定向

```php
Route::redirect('/', 'path')->name('root');
```

### 回退路由

回退路由应始终是你应用程序注册的最后一个路由。 

```php
Route::fallback(function () {
});
```

## 定义路由组

### 路由组

```php
Route::group(['middleware' => ['auth', 'second']], function() {
    // 路由
});
```

### 中间件

```php
Route::middleware(['first', 'second'])->group(function () {
    Route::get('/', function () {
        // 使用 first 和 second 中间件
    });

    Route::get('user/profile', function () {
        // 使用 first 和 second 中间件
    });
});
```

### 命名空间

```php
Route::namespace('Admin')->group(function () {
    // 在 「App\Http\Controllers\Admin」 命名空间下的控制器
});
```

### 子域名路由 

```php
Route::domain('{account}.myapp.com')->group(function () {
    Route::get('user/{id}', function ($account, $id) {
    });
});
```

### 路由前缀

```php
Route::prefix('admin')->group(function () {
    Route::get('users', function () {
        // 匹配包含 「/admin/users」 的 URL
    });
});
```

### 路由名称前缀

```php
Route::name('admin.')->group(function () {
    Route::get('users', function () {
        // 指定路由名为 「admin.users」...
    })->name('users');
});
```

## 访问控制

```php
Route::middleware('auth:api', 'throttle:60,1')->group(function () {
    Route::get('/user', function () {
        // 在这里定义的路由每分钟最多 60 次请求
    });
});
```

### 动态访问控制

```php
// 可以根据已验证的 `User` 模型的属性，指定动态请求的最大值
// 如果你的 User 模型包含 rate_limit 属性，则可以将属性名称传递给 throttle 中间件
Route::middleware('auth:api', 'throttle:rate_limit,1')->group(function () {
    Route::get('/user', function () {
        // 在这里定义的路由每分钟最多 rate_limit 次请求
    });
});
```

### 独立访客和认证用户的访问控制

```php
Route::middleware('throttle:10|60,1')->group(function () {
    // 访客每分钟最多 10 次请求
    // 认证用户每分钟最多 60 次请求
});
```

可与动态访问控制结合

```php
Route::middleware('auth:api', 'throttle:10|rate_limit,1')->group(function () {
    Route::get('/user', function () {
    });
});
```

## 表单方法伪造

```php
@method('PUT')
@csrf
```

## 获取当前路由

### 全部信息

```php
Route::current();
```

### 路由名称

```php
Route::currentRouteName(); // users.show
```

### 完整方法名

```php
Route::currentRouteAction(); // App\Http\Controllers\QuestController@show
```

## 存在判断

```php
Route::has('register');
```

## 延伸阅读

- [中文文档](https://learnku.com/docs/laravel/6.x/routing/5135)

