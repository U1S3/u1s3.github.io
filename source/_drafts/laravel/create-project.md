---
title: 开始一个新应用
date: 2017-12-28 22:37:28
categories: Laravel
---

{% fullimage /uploads/laravel/create-project/create-project.jpg %}

<!-- more -->

## 创建应用

```bash
# 指定版本 --prefer-dist 强制使用压缩包，而不是克隆源代码
$ composer create-project laravel/laravel my-project --prefer-dist "6.*"
# 最新版本 缺点：无法更改源 下载慢
$ laravel new my-project
```

如果在执行 `laravel new`  操作时遇到 `zsh: command not found: laravel` ，可执行下面操作

```bash
export PATH="$HOME/.composer/vendor/bin:$PATH"
```

## 配置站点

*homestead.yaml*

```yaml
sites:
    - map: my-project.test
      to: /home/vagrant/code/my-project/public

databases:
    - my-project
```

更新配置

```bash
$ vagrant reload --provision
```

## 配置应用

*.env*

```env
APP_NAME="My Project"
APP_URL=http://my-project.test
DB_DATABASE=my-project
DB_USERNAME=homestead
DB_PASSWORD=secret
```

*.gitignore*

```gitignore
...
/public/mix-manifest.json
/public/js
/public/css
```

*.editorconfig*

```
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
indent_style = space
indent_size = 4
trim_trailing_whitespace = true

[*.md]
trim_trailing_whitespace = false

[*.{yml,yaml}]
indent_size = 2

[*.{js,html,blade.php,css,scss,styl}]
indent_style = space
indent_size = 2
```

*config/app.php*

```php
'timezone' => 'Asia/Shanghai',
'locale' => 'zh-CN',
```

##  创建助手函数

```bash
$ touch app/helpers.php
```

*composer.json*

```php
"autoload": {
    "files": [
        "app/helpers.php",
    ],
},
```

刷新自动加载

```bash
$ composer dump-autoload
```

## 移动模型

```bash
$ mkdir -p app/Models && mv app/User.php app/Models
```

全局替换 `App\User` 为 `App\Models\User`

修改 `app/Models/User.php` 的命名空间为 `App\Models;` 

