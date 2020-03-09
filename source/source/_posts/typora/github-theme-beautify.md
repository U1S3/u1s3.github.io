---
title: Typora『Github』主题美化
date: 2019-10-27 10:58:14
categories: [Typora]
tags: [Typora, 主题]
---

> Typora 自带的 Github 主题样式是我喜欢的简洁样式，但是标题这一块儿不是太喜欢，自己调整了一下，和本站相似。

<!-- more -->

*Typora/themes/github.user.css*

```css
body {
    font-family: 'Lato', "PingFang SC", "Microsoft YaHei", sans-serif;
}

#write h1,
#write h2,
#write h3,
#write h4,
#write h5,
#write h6 {
    font-weight: normal;
}

#write h1:before,
#write h2:before,
#write h3:before,
#write h4:before {
    width: 12px;
    display: inline-block;
    margin-right: 6px;
    text-align: center;
    color: #fc6423;
}

#write h1 {
    font-size: 24px;
    border-bottom: 1px solid #f5f5f5;
    padding-bottom: 7.2px;
}

#write h2 {
    font-size: 22px;
    border-bottom: 0;
}

#write h2:before {
    content: '#';
}

#write h3 {
    font-size: 20px;
}

#write h3:before {
    content: '|';
    position: relative;
}

#write>h3.md-focus:before {
    top: 1px;
    left: 0;
}

#write h4 {
    font-size: 18px;
}

#write h4:before {
    content: '!';
}

#write>h4.md-focus:before {
    position: unset;
    top: 0;
    left: 0;
}

#write h5:before,
#write h6:before {
    content: '';
}

h3.md-focus:before,
h4.md-focus:before,
h5.md-focus:before,
h6.md-focus:before {
    border: 0;
    font-size: inherit;
    padding-left: 0;
    padding-right: 0;
}

#write pre.md-meta-block {
    border-radius: 0;
}

.md-fences,
code,
tt {
    border-radius: 0;
    border: 0;
}

.code-tooltip {
    box-shadow: unset;
    border: 0;
}
```

