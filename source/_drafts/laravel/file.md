---
title: File『文件管理』
date: 2019-10-06 19:47:06
categories: Laravel
---

{% fullimage /uploads/laravel/file/file.jpg %}

<!-- more -->

> Laravel 的『用户上传文件对象』底层使用了 Symfony 框架的 [UploadedFile](http://api.symfony.com/3.0/Symfony/Component/HttpFoundation/File/UploadedFile.html) 对象进行渲染。

## 上传文件

### 表单声明

在图片或者文件上传时，必须为表单添加声明。

```php
enctype="multipart/form-data"
```

## 文件移动

```php
// $upload_path	目标存储路径
// $filename	文件名
$file->move($upload_path, $filename);
```

## 常用方法

### 获取文件扩展名

```php
$file->getClientOriginalExtension();
```

## 上传案例

### 图片上传

[L02 Laravel 教程 - Web 开发实战进阶 ( Laravel 6.x )](https://learnku.com/courses/laravel-intermediate-training/6.x/upload-avatar/5552)

