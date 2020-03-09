---
title: Manjaro 安装自定义字体
date: 2019-11-08 11:39:32
categories: Manjaro
tags: [Manjaro, 字体]
---

> Manjaro 自带的字体真时没眼看，换一下舒服一点是一点。

## 复制 Windows 字体

复制需要的字体到 `~/.local/share/fonts`

在这个目录中执行：

```bash
$ mkfontscale # 生成索引
$ mkfontdir # 生成索引
$ fc-cache -fv # 更新字体缓存 f = force, v = view
```

## 字体配置

### 系统字体

装了 `wqy-microhei` 字体

![](https://cdn.waixiwai.com/images/20191108114630.png)

### Chrome字体

装了 `Advanced Font Settings` 插件

![](https://cdn.waixiwai.com/images/20191108114606.png)