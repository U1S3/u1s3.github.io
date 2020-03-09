---
title: Controller『控制器』
date: 2018-01-04 11:08:02
categories: Laravel
tags: [控制器]
---

{% fullimage /uploads/laravel/controller/controller.jpg %}

<!-- more -->

## 创建控制器

```bash
$ php artisan make:controller UserController
```

## 渲染模板

### 直接渲染

```php
return view('user.index');
```

### 注入数据

```php
return view('user.index', compact('users'));

return view('user.index', [
    'addresses' => $request->user()->addresses,
]);
```

































## 数据验证

Laravel 控制器中，使用 `$this->validate()` 验证数据的格式

```php
...
public function store (Request $request)
{
  $this->validate([
    'name' => 'required|max:20',
    'email' => 'required|email|unique:users|max:255',
    'password' => 'required|confirmed|min:6'
  ]);
}
...
```

验证方式

| 方式                | 说明                                       |
| ----------------- | ---------------------------------------- |
| required          | 不能为空                                     |
| nullable          | 数据为空白时通过验证，一般用于修改资料                      |
| email             | 邮箱格式                                     |
| unique:table_name | 在数据表中唯一，在创建数据表时也要指定唯一                    |
| confirmed         | 验证两次输入是否相同，一般用于密码，需要提交  field_confirmation |
| min:length        | 最小长度                                     |
| max:length        | 最大长度                                     |

多个验证方式使用 `|` 分割

## 创建数据

```php
...
// 参照 模型创建数据
$model = Model::create();
// 例 : $user = User::create(['name' => $request->name]);
// 如果需要获取所有用户输入数据，使用 $request->all()
...
```

## 重定向

```php
...
return redirect()->route('route_name', data);
// 例 : return redirect()->route('users.show', [$user]);
// route() 会自动获取主键，等同于 route('users.show', [$user->id])
...
```


