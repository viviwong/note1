---
title: Jekyll框架认识
---

### 为什么要学Jekyll框架

我们涉及到如何把网站做的好看一些的问题。这个需要了解 github Pages 底层的机制。底层 Github Pages 采用了 Jekyll 框架。要把 Github Pages 的网站做漂亮，必须了解 Jekyll 框架如何运行的。

首先，.md 文件，也就是 markdown 格式的支持，就是 Jekyll 提供给我们的。这个前面已经详细介绍过了。

现在，我们来添加 **布局文件**

- 英文参考文档: [官网](https://jekyllrb.com/docs/frontmatter/)

- 中文参考文档(有可能过期): [中文网](http://jekyll.com.cn/docs/frontmatter/)

### Jeckll:添加布局文件

首先打开所有的 .md 中的头部改成这样

```md
---
title: First Page
layout: default
---
```

然后来创建布局文件 default.html ，这个文件必须存放到 `_layouts` 文件夹之内， `_layouts/default.html` 内容如下:

```html
anything anything
  content
anything anything
```

>注意，上面的 content 外面要套两个大括号。
>
