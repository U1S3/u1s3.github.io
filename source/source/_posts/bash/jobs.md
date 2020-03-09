---
title: Bash 任务处理
date: 2018-01-11 00:19:47
categories: Bash
tags: [Bash, 任务]
---

## 查看列表

```bash
$ jobs -l # 返回任务编号和进程号
```

## 后台运行任务

### 运行中

如果任务尚未运行，直接在命令最后加 `&`：

```bash
$ npm run watch-poll &
```

### 未运行

如果任务已运行，则先挂起任务：

```bash
$ bg %n # %n 为任务编号
```

## 恢复任务到前台

```bash
$ fg %n # %n 为任务编号
```

## 挂起任务

`CTRL + Z`

## 停止任务

`CTRL + C`

