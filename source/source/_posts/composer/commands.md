---
title: Composer 常用命令
date: 2019-10-27 23:08:18
categories: Composer
---

## 扩展管理

### 安装

```bash
$ composer install
$ composer install --no-dev # 不安装仅在开发环境下使用的扩展
```

### 新增

```bash
$ composer require [package]
```

### 移除

```bash
$ composer remove [package]
```

## 自身更新

```bash
$ sudo composer self-update
```

## 清除缓存

```bash
$ composer clear-cache
```

## 延伸阅读

- [命令列表](https://docs.phpcomposer.com/03-cli.html)

