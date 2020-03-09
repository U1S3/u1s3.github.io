---
title: Exception『错误异常』
date: 2018-12-31 14:51:14
categories: Laravel
tags: [错误, 异常]
---

{% fullimage /uploads/laravel/exception/exception.jpg %}

<!-- more -->

## 错误提示

```php
@if (count($errors) > 0)
    @foreach ($errors->all() as $error)
    	{{ $error }}
    @endforeach
@endif
```

## 异常处理

### 抛出异常

```php
throw new \Exception('出错了');
```

### 用户异常处理

```bash
$ php artisan make:exception InvalidRequestException
```

*app/Exceptions/InvalidRequestException.php* 

```php
<?php

namespace App\Exceptions;

use Exception;
use Illuminate\Http\Request;

class InvalidRequestException extends Exception
{
    public function __construct(string $message = "", int $code = 400)
    {
        parent::__construct($message, $code);
    }

    // 异常被触发时系统会调用 render() 方法输出
    public function render(Request $request)
    {
        if ($request->expectsJson()) {
            // json() 方法第二个参数就是 Http 返回码
            return response()->json(['msg' => $this->message], $this->code);
        }

        return view('pages.error', ['msg' => $this->message]);
    }
}
```

*resources/views/page/error.blade.php* 

```php
{{ $msg }}
```

*app/Exceptions/Handler.php* 

```php
protected $dontReport = [
    // 屏蔽指定异常写日志
    InvalidRequestException::class,
];
```

*app/Http/Controllers/UserController.php* 

```php
use App\Exceptions\InvalidRequestException;
throw new InvalidRequestException('出错啦');
```

### 系统异常处理

```bash
$ php artisan make:exception InternalException
```

*app/Exceptions/InternalException.php* 

```php
<?php

namespace App\Exceptions;

use Exception;
use Illuminate\Http\Request;

class InternalException extends Exception
{
    protected $msgForUser;

    public function __construct(string $message, string $msgForUser = '系统内部错误', int $code = 500)
    {
        parent::__construct($message, $code);
        $this->msgForUser = $msgForUser;
    }

    public function render(Request $request)
    {
        if ($request->expectsJson()) {
            return response()->json(['msg' => $this->msgForUser], $this->code);
        }

        return view('pages.error', ['msg' => $this->msgForUser]);
    }
}
```

