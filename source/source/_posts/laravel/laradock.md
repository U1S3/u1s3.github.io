---
title: Laradock 使用踩坑记
date: 2020-01-15 16:39:37
categories: Laravel
---

> 基于已有项目的情况下。

# 安装

在项目根目录执行

```bash
$ git submodule add https://github.com/Laradock/laradock.git
```

> 需要项目已经使用 git 作为版本控制，如果没有，需要使用 git clone 代替 git submoduled

进入 *laradock* 执行

```bash
$ cp env-example .env
```

# 配置 .ENV

## 构建速度慢

**NVM**

```env
WORKSPACE_NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node
```

**NPM**

```env
WORKSPACE_NPM_REGISTRY=https://registry.npm.taobao.org
```

**COMPOSER**

```env
WORKSPACE_COMPOSER_REPO_PACKAGIST=https://mirrors.aliyun.com/composer
```

**WORKSPACE**

```env
CHANGE_SOURCE=true
UBUNTU_SOURCE=aliyun
```

## 项目个性化

**应用名称**

```env
COMPOSE_PROJECT_NAME=laradock_xxx
```

**NGINX端口**

```env
NGINX_HOST_HTTP_PORT=8081
```

# 构建

```bash
$ docker-compose up -d nginx mysql
```

