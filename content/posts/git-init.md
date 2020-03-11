---
title: "Git 初始化"
date: 2017-12-27T20:57:28+08:00
draft: false
recommend: false

tags: ['Git']
categories: ['Programing']
hiddenFromHomePage: true

featuredImage: ""
toc: true
---

## 基本配置

### 用户信息

```bash
$ git config --global user.name "name"
$ git config --global user.email "email@example.com"
```

### 生成 SSH 密钥

```bash
# 一路回车
$ ssh-keygen -t rsa -C "email@example.com"
```

### 默认推送模式

```bash
# 当没有特定分支时自动使用当前分支
$ git config --global push.default simple
```

## 代理配置

[给 Git 配置代理，让 git clone 飞起来](/posts/git-proxy/)