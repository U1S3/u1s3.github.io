---
title: "使用 Travis CI 将 Hugo 站点部署到 Github Pages"
date: 2020-03-07T22:48:05+08:00
draft: true
recommend: true

tags: ['Travis-CI', 'Hugo']
categories: ['Programing']

featuredImage: "https://i.loli.net/2020/03/07/yON5cnJrCL2DGZU.jpg"
toc: true
---

以前使用 Hexo 在 Github Pages 上搭建博客的时候，一直使用 Hexo 自带的 `hexo d` 命令，但是切换到 Hugo 后，没发现类似的命令，那部署就有点麻烦了，要先在本地生成然后在手动提交到仓库。

幸好在刷知乎的时候发现了 Travis CI 这个东西，可以实现将源码提交到仓库后自动部署，感觉发现了新大陆。

<!--more-->

### 准备开始

目的是在一个新的 Github 仓库中，创建 `master` 和 `code` 两个分支，`master` 是 Github Pages 指定的分支，`code` 用于存放源码，之后仅需要提交 `code` 分支，Travis CI 会处理其他工作。

[Hugo 的安装](https://gohugo.io/getting-started/installing/) 和 [站点的创建](https://gohugo.io/getting-started/quick-start/)在官网文档可以找到各种系统的安装方法，不多赘述。

### Travis CI 配置




### 远程仓库

在 Github 上创建名为 `username.github.io` 的新仓库，其中 `username` 是当前登录的 Github 账户的用户名。

### 本地仓库

```bash
$ git init
$ git checkout -b code
# public 文件夹是要推送到 master 分支的站点文件
# 但是因为要使用 Travis CI 自动部署，所以这个文件夹可以忽略不上传
$ echo '/public/' >> .gitignore
$ git add .
$ git commit -m "init"
$ git remote add origin git@github.com:username/username.github.io.git
$ git push -u origin code
```















---

**参考文档**

[Hugo](https://gohugo.io/)

[利用Travis CI和Hugo將Blog自動部署到Github Pages]([https://axdlog.com/zh/2018/using-hugo-and-travis-ci-to-deploy-blog-to-github-pages-automatically/#travis-ci-%E9%85%8D%E7%BD%AE](https://axdlog.com/zh/2018/using-hugo-and-travis-ci-to-deploy-blog-to-github-pages-automatically/#travis-ci-配置))



