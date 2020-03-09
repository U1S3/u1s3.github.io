---
title: Python 开发环境搭建
date: 2019-09-30 23:22:38
categories: Python
tags: [开发环境]
---

## 解释器

### Windows

[Python](https://www.python.org/)

勾选 `Add Python 3.x to PATH`

自定义安装纯英文目录

全选 `Optional Features` 

## 交互式编程工具

安装

```bash
$ pip install ipython
```

启动

```bash
$ ipython
$ python -m IPython
```

## pip

### 国内镜像

`~/pip/pip.ini`

```ini
[global]
index-url = http://mirrors.aliyun.com/pypi/simple/
[install]
trusted-host=mirrors.aliyun.com
```

### 常见错误

You are using pip version...

```bash
$ python -m pip install --user --upgrade pip
```

