---
title: Laravel 短信验证码
comments: true
date: 2019-01-03 14:23:10
summary: 以手机注册为例，EASY-SMS扩展包的使用笔记。
categories: laravel
---

## 新增字段

创建迁移

```bash
$ php artisan make:migration users_add_mobile_and_mobile_verified_at --table=users
```

配置迁移

`database/migrations/[timestamp]_users_add_mobile_and_mobile_verified_at.php`

```php
public function up()
{
    Schema::table('users', function (Blueprint $table) {
        $table->char('mobile', 11)->unique()->after('id')->comment('手机号码');
        $table->dateTime('mobile_verified_at')->nullable()->after('updated_at')->comment('手机号码绑定时间');
    });
}
public function down()
{
    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn('mobile');
        $table->dropCOlumn('mobile_verified_at');
    });
}
```

执行迁移

```bash
$ php artisan migrate
```

配置模型

`app/Models/user.php`

```php
protected $fillable = [
    ..., 'mobile', 'mobile_verified_at'
];
```

## 扩展安装与配置

### 安装

```bash
$ composer require "overtrue/easy-sms"
```

### 配置

```bash
$ touch config/easysms.php
```

```php
return [
    // HTTP 请求的超时时间（秒）
    'timeout' => 5.0,

    // 默认发送配置
    'default' => [
        // 网关调用策略，默认：顺序调用
        'strategy' => \Overtrue\EasySms\Strategies\OrderStrategy::class,

        // 默认可用的发送网关
        'gateways' => [
            'yunpian',
        ],
    ],
    // 可用的网关配置
    'gateways' => [
        'errorlog' => [
            'file' => '/tmp/easy-sms.log',
        ],
        'yunpian' => [
            'api_key' => env('YUNPIAN_API_KEY'),
        ],
    ],
];
```

### 服务提供器

```bash
$ php artisan make:provider EasySmsServiceProvider
```
