---
title: 宝塔面板使用 Composer 踩坑笔记
date: 2019-12-06 10:27:41
categories: Composer
---

# 程序环境

日期：2019-12-06

系统：CentOS linux 8.0.1905

PHP：7.3

# 移除禁用函数

需要移除的函数有两个：

- proc_open
- putenv

# Unzip with unzip command failed, falling back to ZipArchive class

> 释义：无法解压文件夹，回滚到压缩文档。

这个错误主要是因为缺少zip扩展导致的，通过下面放大可解决：

```bash
# 安装 zip 相关扩展
$ yum install zip ubzip
```

因为使用的是宝塔提供的 php7.3 版本，没有整合 zip 模块，切扩展安装面板中也没有 zip 的选项，但因为 zip 扩展本身是包含在软件包里的，所以手动安装一下即可：

```bash
$ cd /www/server/php/73/src/ext/zip/
$ /www/server/php/73/bin/phpize
$ ./configure --with-php-config=/www/server/php/73/bin/php-config
$ make && make install
```

安装成功后需要清理重载 php：

```bash
$ sed -i '/extension = zip.so/d' /www/server/php/73/etc/php.ini
```

然后通过面板重启以下 php 即可。