---
title: Laravel 使用 Easy SMS 发送短信验证码
date: 2019-10-28 16:48:37
categories: Laravel
---

> 使用阿里云短信服务。

# 安装配置

## 安装扩展

```bash
$ composer require overtrue/easy-sms
```

<!-- more -->

## 封装 Provider

> 由于该组件还没有 Laravel 的 ServiceProvider，需要自己封装。 

### 创建配置文件

```bash
$ touch config/easysms.php
```

*config/easysms.php*

```php
<?php

return [
    // HTTP 请求的超时时间（秒）
    'timeout' => 10.0,

    // 默认发送配置
    'default' => [
        // 网关调用策略，默认：顺序调用
        'strategy' => \Overtrue\EasySms\Strategies\OrderStrategy::class,

        // 默认可用的发送网关
        'gateways' => [
            'aliyun',
        ],
    ],
    // 可用的网关配置
    'gateways' => [
        'errorlog' => [
            'file' => '/tmp/easy-sms.log',
        ],
        'aliyun' => [
            'access_key_id' => env('SMS_ALIYUN_ACCESS_KEY_ID'),
            'access_key_secret' => env('SMS_ALIYUN_ACCESS_KEY_SECRET'),
            'sign_name' => 'Larabbs',
        ],
    ],
];
```

### 创建 ServiceProvider 

```bash
$ php artisan make:provider EasySmsServiceProvider
```

*app/providers/EasySmsServiceProvider.php* 

```php
<?php

namespace App\Providers;

use Overtrue\EasySms\EasySms;
use Illuminate\Support\ServiceProvider;

class EasySmsServiceProvider extends ServiceProvider
{
    public function boot()
    {
        //
    }

    public function register()
    {
        $this->app->singleton(EasySms::class, function ($app) {
            return new EasySms(config('easysms'));
        });

        $this->app->alias(EasySms::class, 'easysms');
    }
}
```

### 注册 ServiceProvider 

*config/app.php* 

```php
...
'providers' => [
    ...
    App\Providers\EasySmsServiceProvider::class,
],
...
```

## 配置 AccessKey 

*.env*

```env
# aliyun 短信
SMS_ALIYUN_ACCESS_KEY_ID=
SMS_ALIYUN_ACCESS_KEY_SECRET=
```

# 业务代码











