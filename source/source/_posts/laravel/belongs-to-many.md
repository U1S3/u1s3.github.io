---
title: Laravel 多对多关系实操记录
date: 2019-11-19 16:33:37
categories: Laravel
---

> 背景：日程表 Schedule 和用户表 User，一个日程可以有多个参与用户 attendee，一个用户可以有多个日程。

## 创建中间表

```bash
$ php artisan make:migration create_schedules_attendees_table --create=schedules_attendees
```

*database/migrations/..._create_schedules_attendees_table.php*

```php
public function up()
{
    Schema::create('schedules_attendees', function (Blueprint $table) {
        $table->bigIncrements('id');
        $table->unsignedBigInteger('user_id');
        $table->unsignedBigInteger('schedule_id');
        $table->timestamps();
    });
}
```

```bash
$ php artisan migrate
```

## 注册关联

*app/Models/Schedule.php*

```php
public function attendees()
{
    return $this->belongsToMany(User::class, 'schedules_attendees', 'schedule_id', 'user_id');
}
```

## 关联写入

```php
$schedule->attendees()->attach(\App\Models\User::inRandomOrder()->first());
```

