---
title: Manjaro 安装搜狗输入法
date: 2019-11-07 10:36:00
categories: Manjaro
tags: [Manjaro, 输入法]
---

## 安装软件

```bash
$ yay fcitx-im fcitx-configtool fcitx-sogoupinyin
```

直接回车安装全部。

## 配置文件

```bash
$ sudo vim ~/.xprofile
```

*~/.xprofile*

```
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

重启系统。

