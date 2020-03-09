---
title: Sublime Text 常用扩展
date: 2019-10-27 22:46:05
categories: [Sublime Text]
tags: [Sublime Text, 扩展]
---

## 代码风格

### EditorConfig

*.editorconfig*

```
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
indent_style = space
indent_size = 4
trim_trailing_whitespace = true

[*.md]
trim_trailing_whitespace = false

[*.{yml,yaml}]
indent_size = 2

[*.{js,html,blade.php,css,scss,styl}]
indent_style = space
indent_size = 2
```

## 代码补全

### Emmet

## 远程开发

### SFTP

*Perference->Package Settings->SFTP->setting user*

```json
{
    "email": "xiaosong@xiaosong.me",
    "product_key": "d419f6-de89e9-0aae59-2acea1-07f92a"
}
{
    "email":"Rimke@163.com",
    "product_key":"e83eda-38644b-43c828-e3669b-cd8a85"
}
```

