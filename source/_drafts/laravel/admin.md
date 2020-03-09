---
title: Laravel-admin『管理面板』
date: 2019-01-03 10:56:28
categories: Laravel
---

{% fullimage /uploads/laravel/admin/admin.jpg %}

<!-- more -->

> 本文写于 v1.7.7

## 文档

[官方中文文档](https://laravel-admin.org/docs/zh/)

## 安装扩展

```bash
$ composer require encore/laravel-admin
```

### 发布资源

```bash
# 会生成 config/admin.php 默认配置即可
$ php artisan vendor:publish --provider="Encore\Admin\AdminServiceProvider"
```

### 完成安装

```bash
# 部署阶段无需执行此命令，将开发环境中的与admin相关的数据SQL导入即可。
$ php artisan admin:install
```

### 删除多余文件

多余语言文件

```bash
$ rm -rf resources/lang/ar/ resources/lang/az/ resources/lang/en/admin.php resources/lang/es/ resources/lang/fr/ resources/lang/he/ resources/lang/ja/ resources/lang/nl/ resources/lang/pl/ resources/lang/pt/ resources/lang/ru/ resources/lang/tr/ resources/lang/zh-TW/ resources/lang/pt-BR/ resources/lang/fa/ resources/lang/id/ resources/lang/ms/ resources/lang/uk/ resources/lang/ko/
```

多余控制器

```bash
$ rm -f app/Admin/Controllers/ExampleController.php
```

### 参数配置

根据需要修改 `config/admin.php` 的部分参数

## 后台地址

```url
http://domain.com/admin
```

## 控制器

### 创建控制器

```bash
# 与普通控制器的创建方法稍有区别
# --model=App\\Models\\User 代表新创建的控制器可对指定模型CURD
$ php artisan admin:make UserController --model=App\\Models\\User
```

 *app/Admin/Controllers/UsersController.php* 

```php
// 这里只记录了个别用法

// 页面标题
protected $title = 'App\Models\User';

protected function grid()
{
    // 排序
    $grid->id('ID')->sortable();
    
    // 修改显示内容
    $grid->column('email_verified_at', __('Email verified at'))->display(function ($value) {
        return $value ? '是' : '否';
    });
    
    // 隐藏整个操作列
    $grid->disableActions();
    
    // 隐藏部分按钮
    $grid->actions(function ($actions) {
        // 查看按钮
        $actions->disableView();
        // 删除按钮
        $actions->disableDelete();
        // 编辑按钮
        $actions->disableEdit();
    });
    
    // 隐藏 `新建` 按钮
    $grid->disableCreateButton();
	// 同时在每一行也不显示 `编辑` 按钮
    $grid->disableActions();
    
    // 隐藏 `批量删除` 按钮
    $grid->tools(function ($tools) {
        $tools->batch(function ($batch) {
            $batch->disableDelete();
        });
    });
    
}

protected function form()
{
    $form = new Form(new Product);
    
    // 单行文本
    $form->text('title', __('Title'))->rules('required');
    
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

### 添加路由

*app/Admin/routes.php*

```php
// 添加路由
$router->resource('users', UserController::class);
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

