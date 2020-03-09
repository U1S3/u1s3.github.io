---
title: Mail『邮件发送』
date: 2019-09-26 17:14:01
categories: Laravel
tags: [邮件]
---

{% fullimage /uploads/laravel/mail/mail.jpg  %}

<!-- more -->

## MailHog

> MailHog 是 Homestead 自带的一个组件，可以很方便地调试发送邮件。

### 管理地址

[http://domain.com:8025](http://domain.com:8025)

### 本地调试

*.env*

```env
MAIL_HOST=127.0.0.1
MAIL_PORT=1025
```



















































## 邮箱认证

### 继承接口

```php
use Illuminate\Contracts\Auth\MustVerifyEmail;
class User extends Authenticatable implements MustVerifyEmailContract {}
```

继承接口后可使用：

- `hasVerifiedEmail()` 检测用户 Email 是否已认证；
- `markEmailAsVerified()` 将用户标示为已认证；
- `sendEmailVerificationNotification()` 发送 Email 认证的消息通知，触发邮件的发送；
- `getEmailForVerification()` 获取发送邮件地址，提供这个接口允许你自定义邮箱字段。

### 强制验证邮箱

创建中间件

```bash
$ php artisan make:middleware EnsureEmailIsVerified
```

业务逻辑

`app/Http/Middleware/EnsureEmailIsVerified.php`

```php
public function handle($request, Closure $next)
{
    // 三个判断：
    // 1. 如果用户已经登录
    // 2. 并且还未认证 Email
    // 3. 并且访问的不是 email 验证相关 URL 或者退出的 URL。
    if ($request->user() &&
        ! $request->user()->hasVerifiedEmail() &&
        ! $request->is('email/*', 'logout')) {

        // 根据客户端返回对应的内容
        return $request->expectsJson()
            ? abort(403, 'Your email address is not verified.')
            : redirect()->route('verification.notice');
    }

    return $next($request);
}
```

注册中间件

`app/Http/Kernel.php`

```php
protected $middlewareGroups = [
    'web' => [
        // 注册的时机确保在 StartSession 后面
        \App\Http\Middleware\EnsureEmailIsVerified::class,      // <<--- 只需添加这一行
    ],
];
```

### Links

[3.5. 邮箱认证](https://learnku.com/courses/laravel-intermediate-training/6.x/email-verify/5545)

[Laravel 6 中文文档](https://learnku.com/docs/laravel/6.x/verification/5154)

## 自定义邮件发送

```php
use Mail;
...
// 发送方法
public function sendEmailConfirmationTo($user)
{
    // 包含邮件消息的视图名称
    $view = 'emails.confirm';
    // 要传递给该视图的数据数组
    $data = compact('user');
    $from = 'chishui@example.com';
    $name = 'chishui';
    $to = $user->email;
    $subject = "感谢注册 Weibo 应用！请确认你的邮箱。";

    Mail::send($view, $data, function($message) use ($from, $name, $to, $subject) {
        $message->from($from, $name)->to($to)->subject($subject);
    });
}
// 发送
public function store(Request $request)
{
    $this->sendEmailConfirmationTo($user);
}
```

## 生产环境

[在生产环境中发送邮件](https://learnku.com/courses/laravel-essential-training/5.8/in-a-production-environment-to-send-mail/4105)









