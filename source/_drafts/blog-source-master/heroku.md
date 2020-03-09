---
title: Heroku
date: 2017-12-28 23:03:32
categories: 说明书
tags: Heroku
---

### 登陆

```bash
$ heroku login
```

### 新建

```bash
$ heroku create # 创建应用
$ echo web: vendor/bin/heroku-php-apache2 public/ > Procfile # 配置启动服务器命令
$ heroku buildpacks:set heroku/php # 声明应用环境
$ php artisan key:generate # 生成 APP_KEY
$ heroku config:set APP_KEY= # 替换 APP_KEY
```

### 推送

```bash
$ git push heroku master
$ heroku open # 打开应用
$ heroku domains # 应用列表
```

### 连接

```bash
$ git remote add heroku https://git.heroku.com/app_name.git
```

### 应用改名

```bash
$ heroku rename your-app-name
```

### 系统日志

```bash
$ heroku logs
```

## 初次使用

### 添加 SSH Key

```bash
$ heroku keys:add # 此处需要 .ssh 文件夹内有 .pub 文件
```

