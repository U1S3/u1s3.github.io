---
title: 在 Windows Terminal 中使用 Git Bash
date: 2019-10-25 11:13:08
categories: [Windows Terminal]
tags: [Windows Terminal, Git Bash]
---

![](https://cdn.waixiwai.com/images/20191027160518.jpg)

<!-- more -->


## 新增系统环境变量

![](https://cdn.waixiwai.com/images/20191027160607.jpg)

## 打开配置文件

![](https://cdn.waixiwai.com/images/20191027160546.jpg)

## 新增控制台

```json
"profiles" : 
[
    ...
    {
        "acrylicOpacity" : 0.75,
        "closeOnExit" : true,
        "colorScheme" : "Minttyrc",
        "commandline" : "bash.exe",
        "cursorColor" : "#d9e6f2",
        "cursorShape" : "underscore",
        "fontFace" : "Microsoft YaHei Mono",
        "fontSize" : 12,
        "guid" : "{c6eaf9f4-32a7-5fdc-b5cf-066e8a4b1e40}",
        "historySize" : 9001,
        "icon" : "C:\\Users\\CHISHUI\\Pictures\\Git.png",
        "name" : "git-bash",
        "padding" : "20, 0, 0, 0",
        "snapOnInput" : true,
        "startingDirectory" : "%USERPROFILE%",
        "useAcrylic" : true,
        "backgroundImage" : "C:\\Users\\CHISHUI\\Pictures\\ycy.jpg",
        "backgroundImageOpacity" : 0.1,
        "backgroundImageStrechMode" : "fill"
    }
],
```

## 新增主题

```json
"schemes" : 
[
    ...
    {
        "background" : "#0d1926",
        "black" : "#000000",
        "blue" : "#7a7ab8",
        "brightBlack" : "#262626",
        "brightBlue" : "#bdbddb",
        "brightCyan" : "#bddbdb",
        "brightGreen" : "#bddbbd",
        "brightPurple" : "#dbbddb",
        "brightRed" : "#dbbdbd",
        "brightWhite" : "#ffffff",
        "brightYellow" : "#dbdbbd",
        "cyan" : "#7ab8b8",
        "foreground" : "#d9e6f2",
        "green" : "#7ab87a",
        "name" : "Minttyrc",
        "purple" : "#b87ab8",
        "red" : "#b87a7a",
        "white" : "#d9d9d9",
        "yellow" : "#b8b87a"
    }
]
```

## 配置默认启动

```json
"globals" : 
{
    "defaultProfile" : "{c6eaf9f4-32a7-5fdc-b5cf-066e8a4b1e40}",
    ...
},
```

## Git 图标

![](https://cdn.waixiwai.com/images/20191027160618.png)

