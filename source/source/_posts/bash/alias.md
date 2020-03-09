---
title: 给 Bash 命令设置别名
date: 2018-01-11 00:19:47
categories: Bash
tags: [Bash, 别名]
---

> 开发过程中经常会输入命令行来操作，有一些常用的命令可以通过简单的配置来简化输入。

在不同的系统中配置文件不同：

- Windows： *~/.bash_profile*
- Linux： *~/.bashrc*  或 *~/.zshrc*

```bash
# 注意：『=』号前后不能加空格
alias chishui='ssh chishui@example.com -p 22'
```

