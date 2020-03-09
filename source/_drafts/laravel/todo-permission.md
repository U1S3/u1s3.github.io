---
title: 权限管理『Permission』
date: 2019-10-12 09:39:56
categories: Laravel
tags: [权限管理, Permission]
---

{% fullimage /uploads/laravel/permission/permission.jpg %}

<!-- more -->

## laravel-permission

### 环境准备

安装扩展

```bash
$ composer require spatie/laravel-permission
```

生成迁移文件

```bash
$ php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider" --tag="migrations"
```

执行迁移

```bash
$ php artisan migrate
```

生成配置信息

````bash
$ php artisan vendor:publish --provider="Spatie\Permission\PermissionServiceProvider" --tag="config"
````

加载 HasRoles

`app/Models/User.php`

```php
use Spatie\Permission\Traits\HasRoles;
class User extends Authenticatable
{
    use HasRoles;
}
```

### 初始化角色权限

#### 迁移方式

创建迁移

```bash
$ php artisan make:migration seed_roles_and_permissions_data
```

创建权限及角色

```php
use Illuminate\Database\Eloquent\Model;
use Spatie\Permission\Models\Role;
use Spatie\Permission\Models\Permission;

class SeedRolesAndPermissionsData extends Migration
{
    public function up()
    {
        // 清除缓存
        app(Spatie\Permission\PermissionRegistrar::class)->forgetCachedPermissions();

        // 创建权限
        Permission::create(['name' => 'manage_contents']);

        // 创建角色，并赋予权限
        $founder = Role::create(['name' => 'Founder']);
        $founder->givePermissionTo('manage_contents');
    }

    public function down()
    {
        // 清除缓存
        app(Spatie\Permission\PermissionRegistrar::class)->forgetCachedPermissions();

        // 清空所有数据表数据
        $tableNames = config('permission.table_names');

        Model::unguard();
        DB::table($tableNames['role_has_permissions'])->delete();
        DB::table($tableNames['model_has_roles'])->delete();
        DB::table($tableNames['model_has_permissions'])->delete();
        DB::table($tableNames['roles'])->delete();
        DB::table($tableNames['permissions'])->delete();
        Model::reguard();
    }
}
```

赋予权限

` database/seeds/UsersTableSeeder.php `

```php
class UsersTableSeeder extends Seeder
{
    public function run()
    {
        // 将 1 号用户指派为『站长』
        $user = User::find(1);
        $user->assignRole('Founder');
    }
}
```

### 基础用法

 新建角色 

```php
use Spatie\Permission\Models\Role;
$role = Role::create(['name' => 'Founder']);
```

新建权限，并赋予角色

```php
use Spatie\Permission\Models\Permission;
Permission::create(['name' => 'manage_contents']);
$role->givePermissionTo('manage_contents');
```

赋予用户角色

```php
// 单个角色
$user->assignRole('Founder');

// 多个角色
$user->assignRole('writer', 'admin');

// 数组形式的多个角色
$user->assignRole(['writer', 'admin']);
```

检查角色

```php
// 是否是站长
$user->hasRole('Founder');

// 是否拥有至少一个角色
$user->hasAnyRole(Role::all());  

// 是否拥有所有角色
$user->hasAllRoles(Role::all());   
```

检查权限

```php
// 检查用户是否有某个权限
$user->can('manage_contents'); 

// 检查角色是否拥有某个权限
$role->hasPermissionTo('manage_contents');  
```

直接赋予权限

```php
// 为用户添加『直接权限』
$user->givePermissionTo('manage_contents');

// 获取所有直接权限
$user->getDirectPermissions() 
```

### 部署权限













