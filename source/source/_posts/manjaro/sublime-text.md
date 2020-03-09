---
title: 在 Manjaro 中安装 Sublime Text 3
date: 2019-11-07 16:42:32
categories: 
	- [Manjaro]
	- [Sublime Text]
tags: [Manjaro, Sublime Text]
---

## 安装 GPG key

```bash
$ curl -O https://download.sublimetext.com/sublimehq-pub.gpg && sudo pacman-key --add sublimehq-pub.gpg && sudo pacman-key --lsign-key 8A8F901A && rm sublimehq-pub.gpg
```

## 指定版本

### 稳定版

```bash
$ echo -e "\n[sublime-text]\nServer = https://download.sublimetext.com/arch/stable/x86_64" | sudo tee -a /etc/pacman.conf
```

### 开发版

```bash
$ echo -e "\n[sublime-text]\nServer = https://download.sublimetext.com/arch/dev/x86_64" | sudo tee -a /etc/pacman.conf
```

## 开始安装

```bash
$ sudo pacman -Syu sublime-text
```

## 激活码

[Sublime Text 3 激活码](/sublime-text/license/)

