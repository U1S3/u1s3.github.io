---
title: Hexo
comments: false
date: 2017-12-22 14:36:27
keywords: hexo
description: Hexo的使用方法。
summary: Hexo的使用方法。
categories: 工具箱
tags: [hexo]
---

## 日常使用

### 文章发布

```bash
$ hexo n "article name" # 新建文章 n == new
$ hexo g # 生成页面 g == generate
$ hexo g -d -w # 生成后立即部署网站并监视文件变动
$ hexo d # 部署 d == depoly
```

### 本地服务

```bash
$ hexo s # 启动服务预览 s == server
$ hexo s -s # 静态模式 -s == --static
$ hexo s -p  5000 # 更改端口
$ hexo s -l # 启动日志记录 -l == --log
$ hexo s -i 192.168.1.1 #自定义ip
```

### 清除缓存

```bash
$ hexo clean # 正常情况下可忽略此命令
```

## 初次使用

### 初始化博客

```bash
$ yarn global add hexo-cli # 安装hexo-cli
$ yarn global upgrade hexo-cli # 升级hexo-cli
$ hexo init myblog # 初始化博客 myblog为博客文件夹名
```

### 关联 hexo 与 github

```javascript
// 编辑站点配置文件 _config.yml
deploy:
  type: git
  repo: git@github.com:one-three/one-three.github.io.git
  branch: master
```

### 安装 git 部署插件

```bash
$ yarn add hexo-deployer-git
```

## Links

- [官方中文文档](https://hexo.io/zh-cn/docs/commands.html)

- [GitHub+Hexo 搭建个人网站详细教程](https://zhuanlan.zhihu.com/p/26625249)

- [搭建Hexo博客中碰到的坑](http://www.jianshu.com/p/a2fe56d11c4f)

- [Hexo博客从一台电脑迁移到其他电脑](https://www.jianshu.com/p/beb8d611340a)

