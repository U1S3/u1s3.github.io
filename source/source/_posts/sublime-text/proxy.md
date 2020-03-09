---
title: 给 Sublime Text 的 Package Control 设置代理
date: 2019-10-27 22:34:11
categories: [Sublime Text]
tags: [Sublime Text, 代理]
---

> 前提是得有梯子。

*Preferences->Package Settings->Package Control->Settings - User*

```json
"http_proxy": "http://127.0.0.1:1080",
"https_proxy": "http://127.0.0.1:1080",
```

