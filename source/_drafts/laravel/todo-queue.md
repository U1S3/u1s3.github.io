---
title: 队列『Queue』
date: 2019-10-09 23:21:01
categories: Laravel
tags: [队列, Queue]
---

{% fullimage /uploads/laravel/queue/queue.jpg  %}

<!-- more -->

>  Laravel 开始推荐使用 `phpredis` 来代替 `predis`。
> 所以要记得先安装 `phpredis`, 然后在 `config/app.php` 中去掉 `Redis` 这个别名。 
>
> https://learnku.com/laravel/t/33538 





## Redis

### 安装扩展

[predis/predis](https://github.com/nrk/predis)

```bash
$ composer require predis/predis
```

`.env`

```env
QUEUE_CONNECTION=redis
REDIS_CLIENT=predis
```

### 生成任务类

```bash
$ php artisan make:job TranslateSlug
```

`app/Jobs/TranslateSlug.php`

```php
use App\Models\Topic;
use App\Handlers\SlugTranslateHandler;
...
class TranslateSlug implements ShouldQueue
{
    ...
    protected $topic;

    public function __construct(Topic $topic)
    {
        // 队列任务构造器中接收了 Eloquent 模型，将会只序列化模型的 ID
        $this->topic = $topic;
    }

    public function handle()
    {
        // 请求百度 API 接口进行翻译
        $slug = app(SlugTranslateHandler::class)->translate($this->topic->title);

        // 为了避免模型监控器死循环调用，我们使用 DB 类直接对数据库进行操作
        \DB::table('topics')->where('id', $this->topic->id)->update(['slug' => $slug]);
    }
}

```

### 推送任务到队列

```php
dispatch(new TranslateSlug($topic));
```

###启动队列并监听

```bash
$ php artisan queue:listen
```

### Links

[使用队列](https://learnku.com/courses/laravel-intermediate-training/6.x/using-queues/5576)

## 队列监控

### Horizon

[laravel/horizon](https://github.com/laravel/horizon)

```bash
$ composer require laravel/horizon
$ php artisan vendor:publish --provider="Laravel\Horizon\HorizonServiceProvider"
```

访问权限

`app/Providers/AuthServiceProvider.php`

```php
public function boot()
{
    \Horizon::auth(function ($request) {
        // 是否是站长
        return \Auth::user()->hasRole('Founder');
    });
}
```

