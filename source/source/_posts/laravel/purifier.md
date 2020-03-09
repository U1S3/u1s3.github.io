---
title: Laravel 使用 HTMLPurifier 防范 XSS 攻击
date: 2019-11-1 17:31:14
categories: Laravel
---

> 永远不要信任用户提交的数据。 

## 安装使用

```bash
$ composer require mews/purifier
```

开箱即用，使用 `clean` 就能过滤掉指定标签：

```php
clean(Input::get('inputname'));
```

## 自定义配置

如果需要自定义过滤规则，可以通过发布命令发布资源：

```bash
$ php artisan vendor:publish --provider="Mews\Purifier\PurifierServiceProvider"
```

*config/purifier.php*

```php
return [
    'encoding'      => 'UTF-8',
    'finalize'      => true,
    'cachePath'     => storage_path('app/purifier'),
    'cacheFileMode' => 0755,
    'settings'      => [
        'default' => [
            'HTML.Doctype'             => 'XHTML 1.0 Transitional',
            'HTML.Allowed'             => 'div,b,strong,i,em,a[href|title],ul,ol,ol[start],li,p[style],br,span[style],img[width|height|alt|src],*[style|class],pre,hr,code,h2,h3,h4,h5,h6,blockquote,del,table,thead,tbody,tr,th,td',
            'CSS.AllowedProperties'    => 'font,font-size,font-weight,font-style,margin,width,height,font-family,text-decoration,padding-left,color,background-color,text-align',
            'AutoFormat.AutoParagraph' => true,
            'AutoFormat.RemoveEmpty'   => true,
        ],
    ],
];
```

## 通过 Observe 自动过滤

### 创建注册

```bash
$ php artisan make:observer DiaryObserver
```

*app/Providers/AppServiceProvider.php*

```php
public function boot()
{
    Diary::observe(DiaryObserver::class);
}
```

### 配置自动过滤

*app/Observers/DiaryObserver.php*

```php
<?php

namespace App\Observers;

use App\Models\Diary;

class DiaryObserver
{
    public function saving(Diary $diary)
    {
        $diary->body = clean($diary->body);
    }
}
```

