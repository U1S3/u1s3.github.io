---
title: Manjaro 初始化配置
date: 2019-09-10 18:03:32
categories: Manjaro
tags: [Manjaro, 初始化]
---

## 切换国内镜像

```bash
$ sudo pacman-mirrors -i -c China -m rank # 选择清华源
```

添加源

```bash
$ sudo vim /etc/pacman.conf
```

*/etc/pacman.conf*

```.conf
# 写在最后
[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

## 刷新缓存

```bash
$ sudo pacman -Syy
```

## 导入公钥

```bash
$ sudo pacman -S archlinuxcn-keyring
```

## 更新系统

```bash
$ sudo pacman -Syyu
```

## 更改系统字体

安装文泉驿黑体 `wqy-microhei`，通过系统字体管理修改字体。

## 开启 SSH

开机启动

```bash
$ systemctl enable sshd.service 
```

立即启动

```bash
$ systemctl start sshd.service 
```

立即重启

```bash
$ systemctl restart sshd.service 
```

