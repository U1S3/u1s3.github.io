---
title: 程序安全『Security』
date: 2018-12-22 10:43:39
categories: Laravel
tags: [程序安全, Security]
---

{% fullimage /uploads/laravel/security/security.jpg %}

<!-- more -->

## XSS

XSS 也称跨站脚本攻击 (Cross Site Scripting)，恶意攻击者往 Web 页面里插入恶意 JavaScript 代码，当用户浏览该页之时，嵌入其中 Web 里面的 JavaScript 代码会被执行，从而达到恶意攻击用户的目的。

一种比较常见的 XSS 攻击是 Cookie 窃取。我们都知道网站是通过 Cookie 来辨别用户身份的，一旦恶意攻击者能在页面中执行 JavaScript 代码，他们即可通过 JavaScript 读取并窃取你的 Cookie，拿到你的 Cookie 以后即可伪造你的身份登录网站。

有两种方法可以避免 XSS 攻击：

- 对用户提交的数据进行过滤。

- Web 网页显示时对数据进行特殊处理，一般使用 `htmlspecialchars()` 输出。

### HTMLPurifier

运用『白名单机制』对 HTML 文本信息进行 XSS 过滤，一般写进观察器里在数据入库前过滤即可。

[HTMLPurifier](http://htmlpurifier.org/) 是一个独立的项目，运用『白名单机制』对 HTML 文本信息进行 XSS 过滤。这种过滤机制可以有效地防止各种 XSS 变种攻击。只通过我们认为安全的标签和属性，对于未知的全部过滤。

[mews/purifier](https://packagist.org/packages/mews/purifier)

#### 安装

```bash
$ composer require mews/purifier
```

#### 规则

可使用自带规则，也可自定义，可根据不同的情境调用不同的配置参数。

```bash
php artisan vendor:publish --provider="Mews\Purifier\PurifierServiceProvider"
```

`config/purifier.php`

可直接使用下面的规则

```php
return [
    'encoding'      => 'UTF-8',
    'finalize'      => true,
    'cachePath'     => storage_path('app/purifier'),
    'cacheFileMode' => 0755,
    'settings'      => [
        'user_topic_body' => [
            'HTML.Doctype'             => 'XHTML 1.0 Transitional',
            'HTML.Allowed'             => 'div,b,strong,i,em,a[href|title],ul,ol,ol[start],li,p[style],br,span[style],img[width|height|alt|src],*[style|class],pre,hr,code,h2,h3,h4,h5,h6,blockquote,del,table,thead,tbody,tr,th,td',
            'CSS.AllowedProperties'    => 'font,font-size,font-weight,font-style,margin,width,height,font-family,text-decoration,padding-left,color,background-color,text-align',
            'AutoFormat.AutoParagraph' => true,
            'AutoFormat.RemoveEmpty'   => true,
        ],
    ],
];
```

#### 过滤

```php
$topic->body = clean($topic->body, 'default');
```

### Links

[IBM 文档库：跨站点脚本攻击深入解析](https://www.ibm.com/developerworks/cn/rational/08/0325_segal/)

[如何在使用 htmlpurifier 的情况下嵌入 youtu 视频?](https://laravel-china.org/topics/18568)

[在编辑器已经帮我们过滤的情况下，仍需要这样的三方工具么？](https://laravel-china.org/topics/19478)

[浅谈XSS攻击的那些事（附常用绕过姿势）](https://zhuanlan.zhihu.com/p/26177815)

[XSS攻击常识及常见的XSS攻击脚本汇总](https://lvtao.net/dev/xss.html)

## 数据安全

### 外键约束

```php
// 当 user_id 对应的 users 表数据被删除时，删除词条
$table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');
```

 https://learnku.com/courses/laravel-intermediate-training/6.x/delete-user/5597 

##  确保 Laravel 网站不会被嵌入到其他站点中 

 https://learnku.com/articles/35201 

