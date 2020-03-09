---
title: Git 操作本地仓库
date: 2019-11-9 23:28:32
categories: Git
---

## 新建本地仓库

```bash
$ git init
```

## 添加到本地暂存区

```bash
$ git add -A
$ git commit -m "sth."
```

## 推送到远程仓库

### 首次推送

```bash
$ git remote add origin git@github.com:username/repository.git
$ git push -u origin master
```

### 非首次

```bash
$ git push
```

## 分支操作

### 新建

```bash
$ git checkout -b branch
```

### 切换

```bash
$ git checkout branch
```

### 合并

```bash
$ git merge branch
```

### 删除

```bash
$ git branch -d branch
```

### 修改提交信息

```bash
$ git commit --amend
```

## 撤销修改

### 还原修改文件

```bash
$ git checkout .
```

### 撤销暂存区更改

```bash
$ git add -A
$ git checkout -f
```

### 清理应用

```bash
$ git clean -f -d # -f 强制清理文件 -d 连文件夹一并清除
```

### 删除版本库已提交文件

已经在版本库里的文件无法直接通过 `.gitignore` 排除。

```bash
$ git rm --cached
```

## 状态查询

### 暂存区状态

```bash
$ git status
```

### 历史提交记录

```bash
$ git log
```

## 忽略文件

### 当前文件夹

```gitignore
*
!.gitignore
```


