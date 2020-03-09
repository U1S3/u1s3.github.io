---
title: Git 操作远程仓库
date: 2019-11-9 23:45:50
categories: Git
---

## 远程地址

```bash
$ git remote -v # -v 显示完整仓库地址
```

## 克隆仓库

### 主分支

```bash
$ git clone github@github.com:username/repository.git
```

### 指定分支

```bash
$ git clone -b branch github@github.com:username/repository.git
```

## 更换仓库

```bash
git remote set-url origin github@github.com:username/repository.git
```

## 移除仓库

```bash
$ git remote rm origin
```

## 远程分支

### 查看

```bash
$ git branch -a
```

### 拉取

查询指定分支在远程仓库的最新状态

```bash
$ git fetch origin branch
```

创建本地新分支链接远程分支

```bash
$ git checkout -b branch origin/branch
```

拉取

```bash
$ git pull origin branch
```

### 删除

```bash
$ git push origin :branch
```



