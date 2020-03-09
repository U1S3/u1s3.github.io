---
title: Migrate『数据库迁移』
date: 2018-12-22 10:43:39
categories: Laravel
---

{% fullimage /uploads/laravel/migrate/migrate.jpg %}

<!-- more -->

## 迁移操作

### 创建迁移文件

```bash
# 新建数据库
$ php artisan make:migration create_users_table --create=users

# 更新数据库
$ php artisan make:migration users_add_gender_and_introduction --table=users
```

### 执行迁移

```bash
$ php artisan migrate
```

### 回滚迁移

```bash
$ php artisan migrate:rollback
```

### 重置迁移

```bash
$ php artisan migrate:reset
```

### 查看状态

```bash
$ php artisan migrate:status
```

### 重置数据库

重置数据库并重新运行所有迁移

```bash
$ php artisan migrate:refresh
```

## 数据库设计

### 新建数据库

```php
public function up ()
{
  Schema::create('table_names', function(Blueprint $table) {
    $table->bigIncrements('id');
    $table->unsignedBigInteger('user_id');
    $table->timestamps();
  });
}
// 删除数据表
public function down () {
  Schema::dropIfExists('table_names');
}
```

### 更新数据库

```php
public function up()
{
    Schema::table('users', function (Blueprint $table) {
        $table->string('avatar')->nullable();
    });
}
public function down()
{
    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn('avatar');
    });
}
```

## 数据类型

### 自增主键

```php
$table->bigIncrements('id');
$table->unsignedBigInteger('user_id');
```

从 Laravel 5.8 开始，默认的自增 ID 的字段类型从原本的 `int` 改成了 `big int`，相应的 migration 代码应该使用 `bigIncrements()` 方法，定义对应自增 ID 的外键时也需要使用 `unsignedBigInteger()` 方法。 

### 外键约束

```php
// 此表 user_id 字段参照 users 表中的 id 字段 主表删除从表同时删除
$table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');
```

[字段类型](https://learnku.com/docs/laravel/6.x/migrations/5173#columns)

### 金钱相关

```php
$table->decimal('column');
```

与钱相关的有小数点的字段一律使用 `decimal` 类型，而不是 `float` 和 `double`，后面两种类型在做小数运算时有可能出现精度丢失的问题。

### 数值

```php
// 无符号的 int
$table->unsignedInteger('column');

// 无符号的 big int
$table->unsignedBigInteger('column');

// 浮点数
$table->float('column');
```

### 文本

```php
// 短文本
$table->string('column');

// 长文本
$table->text('column');
```

### 布尔值

```php
$table->boolean('column');
```

## 字段修饰

### 默认值

```php
default();
```

[字段修饰](https://learnku.com/docs/laravel/6.x/migrations/5173#column-modifiers)

## DB

### 插入数据

```php
DB::table('categories')->insert($categories);
```

### 清空数据

```php
DB::table('categories')->truncate();
```

