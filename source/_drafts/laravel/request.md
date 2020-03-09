---
title: Request『请求处理』
date: 2018-12-22 10:43:39
categories: Laravel
---

{% fullimage /uploads/laravel/request/request.jpg %}

<!-- more -->

## 请求参数

### 当前登录用户

```php
$request->user();
```

### 全部参数

```php
$request->all();
```

### 部分参数

```php
$request->only([]);
```

### 单个参数

```php
$request->inputName;
```

推荐写法

避免与 Request 对象本身的 `public` 属性冲突，比如 `attributes` / `query` 等

可以很方便地使用 `input()` 的第二个参数来返回默认值

```php
$request->input('content');
```

### 传递数据并跳转

使用 `withInput()` 后在模板里使用 `old('key')` 将能获取到上一次用户提交的内容

```php
return redirect('form')->withInput();
```

## 请求判断

### 路径

```php
// url是否 email/* 或 logout
$request->is('email/*', 'logout');
```

### 格式

```php
// 请求是否json格式
$request->expectsJson();
```

## 请求验证

### 表单请求验证

创建验证文件

```bash
$ php artisan make:request UserRequest
```

*app/Http/Requests/UserRequest.php*

```php
// 定义字段名
public function attributes()
{
    return [
        'province' => '省',
    ];
}

// 定义验证规则
public function rules()
{
    switch ($this->method()) {
        case 'POST':
        case 'PUT':
        case 'PATCH':
        {
            return [
                'photo' => 'nullable|mimes:jpg,png,jpeg',
                'name' => 'required|min:2',
                'id_number' => [
                    'required',
                    'regex:/^(\d{6})(\d{4})(\d{2})(\d{2})(\d{3})([0-9]|X)$/'
                ],
            ];
        }
        case 'GET':
        case 'DELETE':
        default:
        {
            return [];
        }
    }
}

// 定义提示信息
public function messages()
{
    return [
        'photo.mimes' => '「照片」请上传 jpg/png/jpeg 格式的图片',
        'name.required' => '请填写「姓名」',
        'name.min' => '「姓名」应不少于2个字',
        'id_number.required' => '请填写「身份证号」',
        'id_number.regex' => '请输入18位「身份证号」,最后一位如是字母请大写',
    ];
}
```

### 简单验证

```php
public function store(Request $request)
{
    $this->validate($request, [
        'name' => 'required|max:50',
        'email' => 'required|email|unique:users|max:255',
        'password' => 'required|confirmed|min:6'
    ]);
    return;
}
```

### 常用规则

[Laravel 6 中文文档](https://learnku.com/docs/laravel/6.x/validation/5144#available-validation-rules)

## 常用方法

### 当前页面连接

```php
Request::url()
```

