---
title: Manjaro 安装配置 SSR 代理
date: 2019-09-10 18:03:32
categories: Manjaro
tags: [Manjaro, SSR, 代理]
---

> Manjaro 中使用小飞机比 Windows 中麻烦一些。

# 安装环境

## 命令行版

### 安装脚本

```bash
# 安装脚本
$ curl https://raw.githubusercontent.com/the0demiurge/CharlesScripts/master/charles/bin/ssr -o "ssr"
# 添加执行权限
$ chmod a+x ssr
$ sudo ln -s /home/chishui/tools/ssr /usr/bin/ssr
# 安装依赖
$ yay -S jq tsocks
# 安装ssr客户端
$ ssr install
```

### 配置参数

```bash
$ vim ~/.local/share/shadowsocksr/config.json
# 打开后直接保存会自动启动代理
$ ssr config
```

*~/.local/share/shadowsocksr/config.json*

**注意：下面所有注释都要删掉**

```json
{
    "server": "",	// 服务器地址
    "server_port": ,	// 代理端口

    "password": "",	// 密码
    "method": "",	// 加密
    "protocol": "",	// 协议
    "protocol_param": "",	// 协议参数
    "obfs": "",	// 混淆
    "obfs_param": "",	// 混淆参数
	...
}
```

### 启动服务

启动

```bash
$ ssr start
```

停止

```bash
$ ssr stop
```

重启

```bash
$ ssr restart
```

卸载

```bash
$ ssr uninstall
```

## 桌面版

```bash
$ yay -S electron-ssr
```

# 代理模式

## PAC

```bash
$ sudo pip install genpac
# 在当前目录下生成 autoproxy.pac
$ genpac --format=pac --pac-proxy="SOCKS5 127.0.0.1:1080" --pac-precise --output="autoproxy.pac"
```

*/etc/environment*

```
auto_proxy="file:///home/username/tools/autoproxy.pac"
```

## SwitchyOmega

### Chrome 安装扩展

在 [Github](https://github.com/FelisCatus/SwitchyOmega) 下载最新版本的 **.crx** 文件，更改后缀为 **.zip** 并解压缩，如果解压失败就换 Windows 系统解压。

打开 Chrome 扩展程序的 **开发者模式**，将解压出来的文件夹拖入后关闭开发者模式。

![](https://cdn.waixiwai.com/images/20191107135814.png)

### 参数配置

配置 proxy

![](https://cdn.waixiwai.com/images/20191107140137.png)

配置 PAC 规则

https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt

![](https://cdn.waixiwai.com/images/20191107143353.png)

切换代理模式

![](https://cdn.waixiwai.com/images/20191107152838.png)