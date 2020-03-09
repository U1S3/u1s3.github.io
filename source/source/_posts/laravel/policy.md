---
title: Laravel 授权策略『Policy』
date: 2018-12-22 10:43:39
categories: Laravel
---

## 创建策略

```bash
$ php artisan make:policy UserPolicy --model=Models/User
```

## 注册策略

### 自动注册

5.8版本之后可使用自动注册，注意：显式映射的任何策略都将优先于自动发现策略。

自动授权假设模型文件存放在 `app` 目录下，如果更改了模型文件的存放目录，还需要进行下面的操作来自定义自动授权注册的规则。

*app/Providers/AuthServiceProvider.php*

```php
public function boot()
{
    $this->registerPolicies();

    // 使用 Gate::guessPolicyNamesUsing 方法来自定义策略文件的寻找逻辑
    Gate::guessPolicyNamesUsing(function ($class) {
        // class_basename 是 Laravel 提供的一个辅助函数，可以获取类的简短名称
        // 例如传入 \App\Models\User 会返回 User
        return '\\App\\Policies\\'.class_basename($class).'Policy';
    });
}
```

### 手动注册

*app/Providers/AuthServiceProvider.php*

```php
protected $policies = [
    'App\Model' => 'App\Policies\ModelPolicy',
    \App\Models\User::class  => \App\Policies\UserPolicy::class,
];
```

<!-- more -->

## 编写策略

```php
public function update(User $currentUser, User $user)
{
    // true 则通过授权，false 则抛出403异常
    return $currentUser->id === $user->id;
}
```

无需检查 `$currentUser` 是不是 `NULL`，未登录用户自动返回 `false`。

默认情况下无需传递当前登录用户，框架会自动加载当前登录用户。

## 使用策略

### 控制器

```php
public function edit(User $user)
{
    // update 为授权方法的名称，$user 为进行授权验证的数据，是授权方法的第二个参数
    // 无权限时抛出 HttpException
    $this->authorize('update', $user);
    return view('users.edit', compact('user'));
}
```

### 视图

```php
@can('update', $user)
...
@endcan
```

