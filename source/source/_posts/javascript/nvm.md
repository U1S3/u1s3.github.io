---
title: 使用 nvm 安装管理 Node.js 版本
date: 2019-11-7 11:48:50
categories: JavaScript
---

## 安装 nvm

### Manjaro

```bash
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.1/install.sh | bash
```

命令行下载不了的话，就直接打开网址另存为 `install.sh`，然后执行 `bash install.sh`。

```
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

## 查看版本

```bash
# 本地版本
$ nvm ls
# 远程版本
$ nvm ls-remote
```

## 安装 node.js

### 最新版本

```bash
$ nvm install node
```

### 指定版本

```bash
$ nvm install 6.14.4
```

### 切换版本

```bash
$ nvm use v6.14.4
```

### 更改默认版本

```bash
$ nvm alias default v6.14.4
```

