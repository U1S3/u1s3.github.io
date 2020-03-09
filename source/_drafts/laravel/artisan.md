---
title: Artisan『命令行』
date: 2018-01-10 22:24:56
categories: Laravel
---

{% fullimage /uploads/laravel/artisan/artisan.jpg %}

<!-- more -->

## 常用命令

| 命令                          | 说明           |
| ---------------------------- | ------------ |
| php artisan key:generate     | 生成 APP_KEY   |
| php artisan make:controller  | 生成控制器        |
| php artisan make:model       | 生成模型         |
| php artisan make:policy      | 生成授权策略       |
| php artisan make:seeder      | 生成 Seeder 文件 |
| php artisan migrate          | 执行迁移         |
| php artisan migrate:rollback | 回滚迁移         |
| php artisan migrate:refresh  | 重置数据库        |
| php artisan db:seed          | 填充数据库        |
| php artisan tinker           | 进入 Tinker 环境 |
| php artisan route:list       | 查看路由列表       |


## 查看帮助
```bash
$ php artisan help [migrate] # 使用 help 查看 migrate 命令的帮助信息
```

## 发布命令

```bash
$ php artisan vendor:publish --provider='Mews\Captcha\CaptchaServiceProvider' 
```

## 新建命令

```bash
# --command 是指定 Artisan 调用的命令
# 推荐为命令加上命名空间，如 larabbs:
$ php artisan make:command CalculateActiveUser --command=larabbs:calculate-active-user
```

*app/Console/Commands/CalculateActiveUser.php*

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use App\Models\User;

class CalculateActiveUser extends Command
{
    // 供我们调用命令
    protected $signature = 'larabbs:calculate-active-user';

    // 命令的描述
    protected $description = '生成活跃用户';

    // 最终执行的方法
    public function handle(User $user)
    {
        // 在命令行打印一行信息
        $this->info("开始计算...");

        $user->calculateAndCacheActiveUsers();

        $this->info("成功生成！");
    }
}
```

执行命令

```bash
$ php artisan larabbs:calculate-active-user
```

