---
title: 数据填充『Seeder』
date: 2018-12-22 10:43:39
categories: Laravel
tags: [数据填充, Seeder]
---

{% fullimage /uploads/laravel/seeder/seeder.jpg %}

<!-- more -->

## 创建填充

```bash
$ php artisan make:seed UsersTableSeeder
```

## 注册填充

`database/seeds/DatabaseSeeder.php`

```php
use Illuminate\Database\Eloquent\Model;

public function run()
{
    Model::unguard();

    $this->call(UsersTableSeeder::class);

    Model::reguard();
}
```

## 编写填充

```php
use App\Models\User;
...
public function run()
{
    $faker = app(Faker\Generator::class);
    // factory 辅助函数 生成使用假数据的用户对象
    // times 指定数量
    // make 创建数据集合
    $users = factory(User::class)->times(50)->make()->each(function($user, $index) use ($faker) {
        $user->name = $faker->name;
    };
    // insert 批量插入
    // makeVisible 临时显示模型文件中的隐藏属性 $hidden
    User::insert($users->makeVisible(['password', 'remember_token'])->toArray());

    // 更新1号用户数据
    $user = User::find(1);
    $user->name = 'chishui';
    $user->email = 'chishui@example.com';
    $user->is_admin = true;
    $user->save();
}
```

## 执行填充

```bash
$ php artisan db:seed # 执行所有Seeder
$ php artisan db:seed --class=UsersTableSeeder # 执行UsersTableSeeder
$ php artisan migrate:refresh --seed # 同时重置数据库和执行Seeder
```

