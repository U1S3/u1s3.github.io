---
title: 解决 Phpstorm 更新时没有写入权限的问题
date: 2019-11-14 10:00:43
categories: [Phpstorm]
tags: [Phpstorm, 权限]
---

Linux 环境下的 Phpstorm 在更新时可能会遇到 `Phpstorm does not have write access to /opt/phpstorm. Please run it by a privileged user to update` 的信息，解决方法也很简单，执行：

```bash
$ sudo chown -R $USER:$USER /opt/phpstorm
```

然后 *Help->Check for Updates* 更新即可。


