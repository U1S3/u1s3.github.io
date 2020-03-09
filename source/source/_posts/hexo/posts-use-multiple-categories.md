---
title: 给 Hexo 文章设置多个分类
date: 2019-10-27 11:47:45
categories: Hexo
tags: [Hexo]
---

有时候在 Hexo 写作中想要给文章使用多个分类，但是下面这种想当然的方法是不行的：

```markdown
categories: [Hexo, Markdown]
```

这种方法会将 `Markdown` 生成为 `Hexo` 的子分类，好在 Hexo 提供了下面的方法来满足需求：

```markdown
categories: 
	- [Hexo]
	- [Markdown, Tags]
```

这里 `Hexo` 与 `Markdown` 是平级的分类，且 `Markdown` 包含一个子类别 `Tags`。

