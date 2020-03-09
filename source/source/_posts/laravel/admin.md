---
title: 使用 Laravel-admin 快速搭建后台管理面板
date: 2019-01-03 10:56:28
categories: Laravel
---

> 本文写于 Laravel-admin v1.7.7 

# 安装扩展

```bash
$ composer require encore/laravel-admin
```

发布资源

```bash
# 会生成 config/admin.php 默认配置即可
$ php artisan vendor:publish --provider="Encore\Admin\AdminServiceProvider"
```

执行安装

```bash
# 部署阶段无需执行此命令，将开发环境中的与admin相关的数据SQL导入即可。
$ php artisan admin:install
```

删除多余语言文件

```bash
$ rm -rf resources/lang/ar/ resources/lang/az/ resources/lang/en/admin.php resources/lang/es/ resources/lang/fr/ resources/lang/he/ resources/lang/ja/ resources/lang/nl/ resources/lang/pl/ resources/lang/pt/ resources/lang/ru/ resources/lang/tr/ resources/lang/zh-TW/ resources/lang/pt-BR/ resources/lang/fa/ resources/lang/id/ resources/lang/ms/ resources/lang/uk/ resources/lang/ko/
```

删除多余控制器

```bash
$ rm -f app/Admin/Controllers/ExampleController.php
```

# 后台地址

```url
http://domain.com/admin
```

# 模型管理

## 控制器

### 创建控制器

```bash
# --model=App\\Models\\Calendar 
# 代表新创建的控制器可对指定模型CURD
$ php artisan admin:make CalendarController --model=App\\Models\\Calendar
```

### 添加到路由

*app/Admin/routes.php*

```php
$router->resource('/calendars', CalendarController::class);
```

### 控制器解析

当前控制器名称

```php
protected $title = '日程分类';
```

数据列表页

```php
protected function grid()
{
    $grid = new Grid(new Calendar);
    // 根据ID排序
    $grid->column('id', 'ID')->sortable();
    
    // 基本输出
    $grid->column('name', '名称');
    
    // 自定义输出
    $grid->column('color', '文字颜色')->display(function($color) {
        return $color;
    });
    
    // 本地化输出
    $grid->column('created_at', __('Created at'));

    // 添加不存的字段列
    $grid->column('full_name')->display(function () {
        return $this->first_name.' '.$this->last_name;
    });
    
    // filter($callback)方法用来设置表格的简单搜索框
    $grid->filter(function ($filter) {
        // 设置created_at字段的范围查询
        $filter->between('created_at', 'Created Time')->datetime();
    });
    
    // 每页数量
    $grid->paginate(15);
    // 设置分页选择器选项
    $grid->perPages([10, 20, 30, 40, 50]);
    
    // 禁用创建按钮
    $grid->disableCreateButton();
    // 禁用分页条
    $grid->disablePagination();
    // 禁用查询过滤器
    $grid->disableFilter();
    // 禁用导出数据按钮
    $grid->disableExport();
    // 禁用行选择checkbox
    $grid->disableRowSelector();
    // 禁用整个操作列
    $grid->disableActions();
    // 禁用部分按钮
    $grid->actions(function ($actions) {
        // 查看按钮
        $actions->disableView();
        // 删除按钮
        $actions->disableDelete();
        // 编辑按钮
        $actions->disableEdit();
    });
    // 禁用 `批量删除` 按钮
    $grid->tools(function ($tools) {
        $tools->batch(function ($batch) {
            $batch->disableDelete();
        });
    });
    
    return $grid;
}
```

数据编辑页

```php
protected function form()
{
    $form = new Form(new Calendar);
    // 仅做显示
    $form->display('id', __('Id'));
    // 单行文本
    $form->text('name', __('Name'));
    // 拾色器
    $form->color('color', __('Color'))->default('#ffffff');
    $form->color('background_color', __('Background color'));

    // 禁用部分按钮
    $form->tools(function (Form\Tools $tools) {
        // 列表按钮
        $tools->disableList();
        // 删除按钮
        $tools->disableDelete();
        // 查看按钮
        $tools->disableView();
        // 添加一个按钮, 参数可以是字符串, 或者实现了Renderable或Htmlable接口的对象实例
    	$tools->add('<a class="btn btn-sm btn-danger"><i class="fa fa-trash"></i>&nbsp;&nbsp;delete</a>');
    });

    // 表单按钮
    $form->footer(function ($footer) {
        // 重置按钮
        $footer->disableReset();
        // 提交按钮
        $footer->disableSubmit();
        // 查看checkbox
        $footer->disableViewCheck();
        // 继续编辑checkbox
        $footer->disableEditingCheck();
        // 继续创建checkbox
        $footer->disableCreatingCheck();
    });
    
    return $form;
}
```













```php
protected function form()
{

    // 多行文本
    $form->textarea('description', __('Description'));

    // 模拟开关 checkout 
    $form->switch('on_sale', __('On sale'))->default(1);
    
    // 浮点数输入框
    $form->decimal('rating', __('Rating'))->default(5.00);
    
    // 数字选择+输入
    $form->number('sold_count', __('Sold count'));

    // 选择图片
    $form->image('image', '封面图片')->rules('required|image');

    // 富文本编辑器
    $form->editor('description', '商品描述')->rules('required');

    // 单选框
    $form->radio('on_sale', '上架')->options(['1' => '是', '0'=> '否'])->default('0');

    // 直接添加一对多的关联模型
    $form->hasMany('skus', 'SKU 列表', function (Form\NestedForm $form) {
        $form->text('title', 'SKU 名称')->rules('required');
        $form->text('description', 'SKU 描述')->rules('required');
        $form->text('price', '单价')->rules('required|numeric|min:0.01');
        $form->text('stock', '剩余库存')->rules('required|integer|min:0');
    });

    // 定义事件回调，当模型即将保存时会触发这个回调
    $form->saving(function (Form $form) {
        $form->model()->price = collect($form->input('skus'))->where(Form::REMOVE_FLAG_NAME, 0)->min('price') ?: 0;
    });

    return $form;
}
```





### 添加到菜单

*Admin->Menu*

![](/uploads/laravel/admin/add_menu.jpg)

## 权限设置

### 添加权限

*Admin->Permissions*

![](/uploads/laravel/admin/add_permissions.jpg)

### 添加角色

*Admin->Roles*

![](/uploads/laravel/admin/add_roles.jpg)

### 添加管理员

*Admin->Users*

![](/uploads/laravel/admin/add_users.jpg)







## Errors

### 图片不显示

```bash
$ php artisan storage:link
```

###  Field type [editor] does not exist. 

V1.7.0之后，富文本编辑器被移除，需要手动安装扩展

```bash
$ composer require jxlwqq/quill
$ php artisan vendor:publish --tag=laravel-admin-quill
```

*config/admin.php*

```php
'extensions' => [
    'quill' => [
        // If the value is set to false, this extension will be disabled
        'enable' => true,
        'config' => [
            'modules' => [
                'syntax' => true,
                'toolbar' =>
                    [
                        ['size' => []],
                        ['header' => []],
                        'bold',
                        'italic',
                        'underline',
                        'strike',
                        ['script' => 'super'],
                        ['script' => 'sub'],
                        ['color' => []],
                        ['background' => []],
                        'blockquote',
                        'code-block',
                        ['list' => 'ordered'],
                        ['list' => 'bullet'],
                        ['indent' => '-1'],
                        ['indent' => '+1'],
                        'direction',
                        ['align' => []],
                        'link',
                        'image',
                        'video',
                        'formula',
                        'clean'
                    ],
            ],
            'theme' => 'snow',
            'height' => '200px',
        ]
    ]
]
```

使用

```php
// 不再使用editor
$form->quill('description', '商品描述')->rules('required');
```

# 数据库备份

## 命令行

### 备份数据

```bash
$ mysqldump -t peach admin_menu admin_permissions admin_role_menu admin_role_permissions admin_role_users admin_roles admin_user_permissions admin_users > database/admin.sql
```

命令解析：

- `-t` 选项代表不导出数据表结构，这些表的结构我们会通过 Laravel 的 migration 迁移文件来创建。
- `peach` 代表我们要导出的数据库名称，后面则是要导出的表列表。
- `> database/admin.sql` 把导出的内容保存到 `database/admin.sql` 文件中。

> 在线上执行 Mysql 命令时则需要在命令行里通过 -u 和 -p 参数指明账号密码，如： `mysqldump -uroot -p123456 peach > database/admin.sql`。

### 恢复数据

```bash
$ mysql peach < database/admin.sql
```

## SHELL

### 备份数据

```bash
$ touch backup_admin_db.sh
```

*backup_admin_db.sh*

```sh
#!/bin/bash

# 导入 .env  环境变量
source ./.env
# 要备份的表
tables="admin_menu admin_permissions admin_role_menu admin_role_permissions admin_role_users admin_roles admin_user_permissions admin_users"
# 执行备份
mysqldump --host="${DB_HOST}" --port=${DB_PORT} --user="${DB_USERNAME}" --password="${DB_PASSWORD}" -t ${DB_DATABASE} ${tables} > database/admin.sql
```

执行权限

```bash
chmod +x back_admin_db.sh
```

执行

```bash
$ ./back_admin_db.sh
```

### 恢复数据

```bash
$ touch recovery_admin_db.sh
```

*recovery_admin_db.sh*

```sh
#!/bin/bash

# 导入 .env  环境变量
source ./.env
# 执行备份
mysql ${DB_DATABASE} < database/admin.sql
```

# 延伸阅读

- [官方文档](https://laravel-admin.org/docs/zh/)