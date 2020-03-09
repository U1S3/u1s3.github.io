---
title: Bash 文件处理
date: 2018-01-11 00:19:47
categories: Bash
tags: [Bash, 文件处理]
---

## 创建

### 文件

```bash
$ touch filename 
```

`touch` 也可用于更新文件时间。

### 文件夹

```bash
# -p --parents 需要时创建上层目录 如已存在则不当做错误
$ mkdir -p dir1/dir2/dir3
```

## 打开

### 当前目录

```bash
$ explorer .
```

