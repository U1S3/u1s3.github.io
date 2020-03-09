---
title: Manjaro 修改目录名为英文
date: 2019-11-07 17:42:32
categories: Manjaro
tags: [Manjaro]
---

```bash
$ vim .config/user-dirs.dirs
```

*user-dirs.dirs*

```
XDG_DESKTOP_DIR="$HOME/Desktop"
XDG_DOWNLOAD_DIR="$HOME/Downloads"
XDG_TEMPLATES_DIR="$HOME/Templates"
XDG_PUBLICSHARE_DIR="$HOME/Public"
XDG_DOCUMENTS_DIR="$HOME/Documents"
XDG_MUSIC_DIR="$HOME/Music"
XDG_PICTURES_DIR="$HOME/Pictures"
XDG_VIDEOS_DIR="$HOME/Videos"
```

修改文件夹名称

```bash
$ mv 桌面 Desktop && mv 下载 Downloads && mv 模板 Templates && mv 公共 Public && mv 文档 Documents && mv 视频 Videos && mv 图片 Pictures && mv 音乐 Music
```

重启电脑。

