---
title: Redis
date: 2019-10-17 16:38:00
categories: Laravel
tags: [Redis]
---

{% fullimage /uploads/laravel/redis/redis.jpg %}

<!-- more -->

> 本文仅适用于在 Laravel6 中使用 Redis

```php
<?php

namespace App\Models\Traits;

use Illuminate\Support\Facades\Redis
use Carbon\Carbon;

trait LastActivedAtHelper
{
    // 缓存相关
    protected $hash_prefix = 'larabbs_last_actived_at_';
    protected $field_prefix = 'user_';

    public function recordLastActivedAt()
    {
        // 获取今天的日期
        $date = Carbon::now()->toDateString();

        // Redis 哈希表的命名，如：larabbs_last_actived_at_2017-10-21
        $hash = $this->hash_prefix . $date;

        // 字段名称，如：user_1
        $field = $this->field_prefix . $this->id;
        
        // 获取所有数据
        // dd(Redis::hGetAll($hash));

        // 当前时间，如：2017-10-21 08:35:15
        $now = Carbon::now()->toDateTimeString();

        // 数据写入 Redis ，字段已存在会被更新
        Redis::hSet($hash, $field, $now);
        
        // 删除数据
        // Redis::del($hash);
    }
}
```

## 同步到数据库

 https://learnku.com/courses/laravel-intermediate-training/6.x/user-last-logon-time/5598 







