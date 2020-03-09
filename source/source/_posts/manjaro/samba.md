---
title: Manjaro 安装配置 Samba
date: 2019-09-10 18:03:32
categories: Manjaro
tags: [Manjaro, Samba]
---

## 安装环境

```bash
$ yay -S samba gvfs-smb manjaro-settings-samba
```

## 配置参数

```bash
$ sudo vim /etc/samba/smb.conf
```

*/etc/samba/smb.conf*

```.conf
...
[myshare]
   comment = myshare
   path = /home/username
   writable = yes
   create mask = 0700
   directory mask = 0700
```

设置samba用户密码

```bash
$ sudo smbpasswd -a username	# 如需新建用户看下面链接[Manjaro 配置Samba 共享文件夹]
```

## 启动服务

### 启动

```bash
$ systemctl enable smb nmb
```

### 开启

```bash
$ systemctl start smb nmb
```

### 重启

```bash
$ systemctl restart smb nmb
```

## 延伸阅读

[Using Samba in your File Manager](https://wiki.manjaro.org/index.php?title=Using_Samba_in_your_File_Manager)

[Manjaro 配置Samba 共享文件夹](http://www.codesimple.cc/post/linux/manjaro-pei-zhi-samba-gong-xiang-wen-jian-jia)

[Linux Samba服务主配文件smb.conf中文详解](https://www.cnblogs.com/fatt/p/5856892.html)

[manjaro linux利用samba与window建立共享文件夹](https://blog.csdn.net/qq_37863891/article/details/90245715)

