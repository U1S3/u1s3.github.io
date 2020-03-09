---
title: Manjaro 安装配置 oh-my-zsh
date: 2019-09-10 18:03:32
categories: Manjaro
tags: [Manjaro, oh-my-zsh]
---

> Manjaro 默认集成了zsh，切换一下终端然后安装 oh-my-zsh就 OK 了。

## 切换终端

```bash
$ cat /etc/shells	# 查看当前已安装的shell种类
$ chsh -s /bin/zsh	# 切换当前用户
$ sudo vim /etc/passwd	# 切换root用户
```

## 安装 oh-my-zsh

安装插件

```bash
$ sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

> 下载不了时，直接另存为 `.sh` 文件，然后执行 `bash install.sh`。

语法高亮

```bash
$ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
$ echo "source ${(q-)PWD}/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ${ZDOTDIR:-$HOME}/.zshrc
$ source ./zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

## 切换主题

```bash
$ sudo vim ~/.zshrc
```

*~/.zshrc* 

```.zshrc
ZSH_THEME="ys"
```

[oh-my-zsh 官方主题库](https://github.com/robbyrussell/oh-my-zsh/wiki/themes)

