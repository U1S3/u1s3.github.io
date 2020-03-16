---
title: "《深入浅出 Docker》读书笔记"
date: 2020-03-16T16:27:52+08:00
draft: true
recommend: false

tags: ['Docker']
categories: ['Programing']
hiddenFromHomePage: false

featuredImage: "https://static.u1s3.com/images/e3fd7b6d8f9b5db1fe6d16598eccaa78.jpg"
toc: true
---

<!--more-->

## Docker 安装

```bash
$ yay -S docker docker-compose
```

安装完成后使用 `$ docker version` 查看服务状态，只有同时输出 Client 和 Server 的信息时才可以。

如果出现 `Got permission denied while trying to connect to the Docker daemon socket...` 之类的错误，就需要先把当前用户加入到 docker 用户组才行。











## 参考文档

