---
title: "给 Git 配置代理，让 git clone 飞起来"
date: 2019-11-09T21:07:17+08:00
draft: false
recommend: false

tags: ['Git']
categories: ['Programing']

featuredImage: ""
toc: true
---







由于某种不可描述的原因，在使用 Github 的时候，无论是 `pull` 还是 `push` 都慢的像骑车上坡一样痛苦。这时候就需要代理了，让他们飞起来。

代理又分两种模式，在使用 https 协议的时候，使用 http 代理模式；在使用 ssh 协议的时候，使用 socks 5 代理模式。

## HTTP 模式

### 全局代理

```bash
# 使用
$ git config --global http.proxy "http://127.0.0.1:1080"
$ git config --global https.proxy "http://127.0.0.1:1080"
# 取消
$ git config --global --unset http.proxy
$ git config --global --unset https.proxy
# 查看
$ git config --global --get http.proxy
$ git config --global --get https.proxy
```

### 局部代理

全局代理的情况下，所有的流量都会走代理。这样不太合适，因为如果使用 npm 的淘宝源之类的国内镜像的话就会有点绕路了。所以设置局部代理是比较好的方式。

```bash
$ git config --global http.https://github.com.proxy 127.0.0.1:1080
$ git config --global https.https://github.com.proxy 127.0.0.1:1080
```

### 协议兼容

如果无法使用 `http` 模式的代理协议，可以将代理写成 `socks5://127.0.0.1:1080`。

## SOCKS 5 模式

SOCKS 5 模式需要创建一个新的文件来保存，不同的系统还需要不同的环境支持。

### 配置文件

```bash
$ touch ~/.ssh/config
```

### Windows

WIN 下无需安装其他软件，直接使用下面的代码就行了。

```
Host github.com *.github.com
  ProxyCommand connect -S 127.0.0.1:1080 -a none %h %p
  User git
  IdentityFile "~/.ssh/id_rsa"
  TCPKeepAlive yes
```

### Manjaro

Manjaro 下需要单独安装一个软件来支持协议。

```bash
$ yay -S connect
```

*~/.ssh/config* 的配置和 Windows 一样。

### Ubuntu

Ubuntu 下需要单独安装一个软件来支持协议。

```bash
$ apt install connect-proxy
```

配置上基本相同，仅修改了软件名称。

```
Host github.com *.github.com
  ProxyCommand connect-proxy -S 127.0.0.1:1080 -a none %h %p
  User git
  IdentityFile "~/.ssh/id_rsa"
  TCPKeepAlive yes
```





