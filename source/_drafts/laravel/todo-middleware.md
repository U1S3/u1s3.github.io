---
title: 中间件『Middleware』
date: 2018-01-13 16:03:31
categories: Laravel
tags: [中间件, Middleware]
---

{% fullimage /uploads/laravel/middleware/middleware.jpg %}

<!-- more -->

## 系统中间件

 *app/Http/Kernel.php* 

```php
<?php

namespace App\Http;

use Illuminate\Foundation\Http\Kernel as HttpKernel;

class Kernel extends HttpKernel
{
    // 全局中间件
    protected $middleware = [
        // 修正代理服务器后的服务器参数
        \App\Http\Middleware\TrustProxies::class,

        // 检测是否应用是否进入『维护模式』
        // 见：https://learnku.com/docs/laravel/5.7/configuration#maintenance-mode
        \App\Http\Middleware\CheckForMaintenanceMode::class,

        // 检测表单请求的数据是否过大
        \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,

        // 对提交的请求参数进行 PHP 函数 `trim()` 处理
        \App\Http\Middleware\TrimStrings::class,

        // 将提交请求参数中空子串转换为 null
        \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,

    ];

    /**
     * The application's route middleware groups.
     *
     * @var array
     */
    protected $middlewareGroups = [

        // Web 中间件组，应用于 routes/web.php 路由文件，
        // 在 RouteServiceProvider 中设定
        'web' => [
            // Cookie 加密解密
            \App\Http\Middleware\EncryptCookies::class,

            // 将 Cookie 添加到响应中
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,

            // 开启会话
            \Illuminate\Session\Middleware\StartSession::class,

            // \Illuminate\Session\Middleware\AuthenticateSession::class,

            // 将系统的错误数据注入到视图变量 $errors 中
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,

            // 检验 CSRF ，防止跨站请求伪造的安全威胁
            // 见：https://learnku.com/docs/laravel/5.7/csrf
            \App\Http\Middleware\VerifyCsrfToken::class,

            // 处理路由绑定
            // 见：https://learnku.com/docs/laravel/5.7/routing#route-model-binding
            \Illuminate\Routing\Middleware\SubstituteBindings::class,

            // 强制用户邮箱认证
            \App\Http\Middleware\EnsureEmailIsVerified::class,

            // 记录用户最后活跃时间
            \App\Http\Middleware\RecordLastActivedTime::class,
        ],

        // API 中间件组，应用于 routes/api.php 路由文件，
        // 在 RouteServiceProvider 中设定
        'api' => [
            // 使用别名来调用中间件
            // 请见：https://learnku.com/docs/laravel/5.7/middleware#为路由分配中间件
            'throttle:60,1',
            'bindings',
        ],
    ];

    // 中间件别名设置，允许你使用别名调用中间件，例如上面的 api 中间件组调用
    protected $routeMiddleware = [

        // 只有登录用户才能访问，我们在控制器的构造方法中大量使用
        'auth' => \Illuminate\Auth\Middleware\Authenticate::class,

        // HTTP Basic Auth 认证
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,

        // 处理路由绑定
        // 见：https://learnku.com/docs/laravel/5.7/routing#route-model-binding
        'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,

        'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,

        // 用户授权功能
        'can' => \Illuminate\Auth\Middleware\Authorize::class,

        // 只有游客才能访问，在 register 和 login 请求中使用，只有未登录用户才能访问这些页面
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,

        // 签名认证，在找回密码章节里我们讲过
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,

        // 访问节流，类似于 『1 分钟只能请求 10 次』的需求，一般在 API 中使用
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,

        // Laravel 自带的强制用户邮箱认证的中间件，为了更加贴近我们的逻辑，已被重写
        'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
    ];

    // 设定中间件优先级，此数组定义了除『全局中间件』以外的中间件执行顺序
    // 可以看到 StartSession 永远是最开始执行的，因为 StartSession 后，
    // 我们才能在程序中使用 Auth 等用户认证的功能。
    protected $middlewarePriority = [
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\Authenticate::class,
        \Illuminate\Routing\Middleware\ThrottleRequests::class,
        \Illuminate\Session\Middleware\AuthenticateSession::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
        \Illuminate\Auth\Middleware\Authorize::class,
    ];
}
```

### auth

见 [Auth](/laravel/auth/)

### throttle

频率限制

```php
// 访问频率 1 分钟内不能超过 6 次
$this->middleware('throttle:6,1')->only('verify', 'resend');
```

### signed

## 自定义中间件

### 创建中间件

```bash
$ php artisan make:middleware RecordLastActivedTime
```

### 注册中间件

*app/Http/Kernel.php*

```php
protected $middlewareGroups = [

    'web' => [
        // 记录用户最后活跃时间
        \App\Http\Middleware\RecordLastActivedTime::class,
    ],
];
```

### 书写中间件

*app/Http/Middleware/RecordLastActivedTime.php*

```php
<?php

namespace App\Http\Middleware;

use Closure;
use Auth;

class RecordLastActivedTime
{
    public function handle($request, Closure $next)
    {
        // 如果是登录用户的话
        if (Auth::check()) {
            // 记录最后登录时间
            Auth::user()->recordLastActivedAt();
        }

        return $next($request);
    }
}
```



























 https://learnku.com/courses/laravel-intermediate-training/6.x/user-last-logon-time/5598 



## 使用中间件

### 在路由中

`routes/web.php`

此处可以直接使用 `mobile_verified` 是因为上面注册过了

```php
Route::group(['middleware' => 'mobile_verified'], function() {
    Route::get('test', function() {
        return '手机号码已验证';
    });
});
```

### 在控制器中

```php
public function __construct()
{
    $this->middleware('auth', [
        'except' => ['show', 'create', 'store']
        // 'only' =>  ['show', 'create', 'store']
    ]);
}
```

`except` 和 `only` 的区别在于 `except` 用于设定指定动作不使用中间件进行过滤， 而 `only` 将只过滤指定动作。

提倡在控制器 `Auth` 中间件使用中，首选 `except` 方法，这样当新增控制器方法时，默认是安全的。

