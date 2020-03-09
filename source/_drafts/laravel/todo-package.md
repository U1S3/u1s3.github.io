---
title: Package『常用扩展』
date: 2018-01-12 11:59:15
categories: Laravel
tags: [常用扩展]
---

{% fullimage /uploads/laravel/package/package.jpg %}

<!-- more -->

## 本地化

### overtrue

[overtrue/laravel-lang](https://github.com/overtrue/laravel-lang)

```bash
$ composer require overtrue/laravel-lang
```

*config/app.php*

```php
'locale' => 'zh-CN',

// 替换
Illuminate\Translation\TranslationServiceProvider::class,
// 为
Overtrue\LaravelLang\TranslationServiceProvider::class,
```

如果需要修改扩展包提供的语言文件，可发布语言文件到项目中。

```bash
$ php artisan lang:publish zh-CN
```

### caouecs

[caouecs/Laravel-lang](caouecs/Laravel-lang)

```bash
$ composer require caouecs/laravel-lang:~4.0 # 5.8-6.0
```

`config/app.php`

```php
'locale' => 'zh-CN',
```

将 `vendor/caouecs/laravel-lang` 下所需语言文件夹复制到 `resources/lang` 下。







## DEBUG

### Laravel Ignition

[Laravel Ignition](https://github.com/facade/ignition) 是 Laravel6.0 之后自带的异常处理扩展 [功能全解析](https://learnku.com/laravel/t/33857)。

### Debugbar

 [laravel-debugbar](https://github.com/barryvdh/laravel-debugbar)

```bash
$ composer require barryvdh/laravel-debugbar --dev
```

## 验证码

### mews

 [mews/captcha](https://github.com/mewebstudio/captcha) 

```bash
$ composer require "mews/captcha:~3.0"
$ php artisan vendor:publish --provider='Mews\Captcha\CaptchaServiceProvider'
```

前端显示

```php
<img src="{{ captcha_src('flat') }}" onclick="this.src='/captcha/flat?'+Math.random()">
```

后台验证

```php
protected function validator(array $data)
{
    return Validator::make($data, [
        'captcha' => ['required', 'captcha'],
    ], [
        'captcha.required' => '验证码不能为空',
        'captcha.captcha' => '请输入正确的验证码',
    ]);
}
```

## 图片处理

### intervention

[Intervention/image](https://github.com/Intervention/image) 

```bash
$ composer require intervention/image
$ php artisan vendor:publish --provider="Intervention\Image\ImageServiceProviderLaravel5"
```

默认使用`GD`库，如果将要开发的项目需要较专业的图片，配置 `config/image.php` 文件：

```php
'driver' => 'ImageMagic'
```

剪裁案例

```php
use Image;
...
public function reduceSize($file_path, $max_width)
{
    // 先实例化，传参是文件的磁盘物理路径
    $image = Image::make($file_path);
    // 进行大小调整的操作
    $image->resize($max_width, null, function ($constraint) {
        // 设定宽度是 $max_width，高度等比例缩放
        $constraint->aspectRatio();
        // 防止裁图时图片尺寸变大
        $constraint->upsize();
    });
    // 对图片修改后进行保存
    $image->save();
}
...
```

## 代码生成器

### summerblue

[summerblue/generator](https://github.com/summerblue/generator)

```bash
$ composer require "summerblue/generator:6.*" --dev
```

整理字段

```php
title:string:index
body:text
user_id:bigInteger:unsigned:index
category_id:integer:unsigned:index
reply_count:integer:unsigned:default(0)
view_count:integer:unsigned:default(0)
last_reply_user_id:integer:unsigned:default(0)
order:integer:unsigned:default(0)
excerpt:text:nullable
slug:string:nullable
```

拼接命令

```bash
$ php artisan make:scaffold Topic --schema="title:string:index,body:text,user_id:bigInteger:unsigned:index,category_id:integer:unsigned:index,reply_count:integer:unsigned:default(0),view_count:integer:unsigned:default(0),last_reply_user_id:integer:unsigned:default(0),order:integer:unsigned:default(0),excerpt:text:nullable,slug:string:nullable"
```

## 当前页

### hieu-le

[letrunghieu/active](https://github.com/letrunghieu)

```bash
$ composer require hieu-le/active
```

如果传参满足指定条件 `$condition` ，此函数将返回 `$activeClass`，否则返回 `$inactiveClass`。

```php
// function active_class($condition, $activeClass = 'active', $inactiveClass = ''){}
{{ active_class((if_route('categories.show') && if_route_param('category', 1))) }}
```

`if_route()` - 判断当前对应的路由是否是指定的路由

`if_route_param()` - 判断当前的 url 有无指定的路由参数

`if_query()` - 判断指定的 GET 变量是否符合设置的值

`if_uri()` - 判断当前的 url 是否满足指定的 url

`if_route_pattern()` - 判断当前的路由是否包含指定的字符

`if_uri_pattern()` - 判断当前的 url 是否含有指定的字符

### summerblue

[summerblue/laravel-active](https://github.com/summerblue-ext-forks/active)

```bash
$ composer require "summerblue/laravel-active:6.*"
```

## HTTP请求

[guzzle/guzzle](https://github.com/guzzle/guzzle)

```bash
$ composer require guzzlehttp/guzzle
```

```php
use GuzzleHttp\Client;
...
// 实例化
$http = new Client;
// 构建请求参数
$query = http_build_query([]);
// 发送 HTTP Get 请求
$response = $http->get($api);
$result = json_decode($response->getBody(), true);
```

## 中文转拼音

[overtrue/pinyin](https://github.com/overtrue/pinyin)

```bash
$ composer require overtrue/pinyin
```

```php
\Str::slug(app(Pinyin::class)->permalink($text));
```

## 代码提示

[barryvdh/laravel-ide-helper](https://github.com/barryvdh/laravel-ide-helper#automatic-phpdocs-for-models)

```bash
$ composer require barryvdh/laravel-ide-helper --dev
```

仅在开发模式加载

`app/Providers/AppServiceProvider.php`

```php
public function register()
{
    if ($this->app->environment() !== 'production') {
        $this->app->register(\Barryvdh\LaravelIdeHelper\IdeHelperServiceProvider::class);
    }
}
```

自动为 Laravel 的 Facades 生成注释

```bash
$ php artisan ide-helper:generate
```

[Laravel 超好用代码提示工具 Laravel IDE Helper](https://learnku.com/articles/10172/laravel-super-good-code-prompt-tool-laravel-ide-helper)

## 用户切换工具

[viacreative/sudo-su]( https://github.com/viacreative/sudo-su )

```bash
$ composer require viacreative/sudo-su
```

添加 Provider

`app/Providers/AppServiceProvider.php`

```php
class AppServiceProvider extends ServiceProvider
{
    public function register()
    {
        if (app()->isLocal()) {
            $this->app->register(\VIACreative\SudoSu\ServiceProvider::class);
        }
    }
}
```

发布资源

```bash
$ php artisan vendor:publish --provider="VIACreative\SudoSu\ServiceProvider"
```

`config/sudosu.php`

```php
return [
    // 允许使用的顶级域名
    'allowed_tlds' => ['dev', 'local', 'test'],

    // 用户模型
    'user_model' => App\Models\User::class
];
```

模板植入

` resources/views/layouts/app.blade.php `

```php
@if (app()->isLocal())
  @include('sudosu::user-selector')
@endif

<!-- Scripts -->
```














# 旧

## 极验验证码

```bash
$ composer require germey/geetest
```

*config/app.php*

```php
...
'providers' => [
  	...
	Germey\Geetest\GeetestServiceProvider::class,
  	...
],
'aliases' => [
  	...
	'Geetest' => Germey\Geetest\Geetest::class,
  	...
]
...
```

```bash
$ php artisan vendor:publish --tag=geetest 
# 生成 config/geetest.php 和 resources/views/vendor/geetest
```

配置 *.env* 文件, 新增 `GEETEST_ID ` `GEETEST_KEY`

在模板文件中使用 `{!! Geetest::render() !!}` 输出验证码

```php
// 添加后台验证
$this->validate($request, [
  ...
  'geetest_challenge' => 'required|geetest',
], [
  ...
  'geetest_challenge.geetest' => config('geetest.server_fail_alert')
]);
```

[中文文档](https://cuiqingcai.com/2988.html)

[In Github](https://github.com/Germey/LaravelGeetest)



## sudo-su

```bash
$ composer require "viacreative/sudo-su:~1.1"
```

## Laravel Administrator

```bash
$ composer require "summerblue/administrator:~1.1"
```


[Laravel-ide-helper 高效的 IDE 智能提示插件](https://laravel-china.org/topics/2532/extended-recommendation-laravel-ide-helper-efficient-ide-smart-tips-plugin)

[Laravel 精选资源大全（持续更新）](https://laravelacademy.org/post/153.html)