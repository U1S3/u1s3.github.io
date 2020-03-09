---
title: Laravel IDE 代码自动补全、提示及跟踪
date: 2020-01-16 10:03:14
categories: Laravel
---

> 本文基于 Laravel 6.*，更多使用方法点击-> [GitHub](https://github.com/barryvdh/laravel-ide-helper)

**安装**

```bash
$ composer require --dev barryvdh/laravel-ide-helper
```

**生成**

```bash
$ php artisan ide-helper:generate
```

**注册自动生成**

*composer.json*

```json
"scripts": {
    "post-update-cmd": [
        "Illuminate\\Foundation\\ComposerScripts::postUpdate",
        "@php artisan ide-helper:generate",
        "@php artisan ide-helper:meta"
    ]
}
```

