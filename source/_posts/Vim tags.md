---
title: Vim tags
categories:
  - Vim
tags:
  - Vim
  - tags
---

tag 是一种可以跳转的标签，要让 tag 相关命令可用，必须先使用 ctags 一类的程序生
成 tag 文件

<!--more-->

跳转光标到 tag
```vim
:ta
:tag
```

## 快捷键

`g<LeftMouse>`
`Control<LeftMouse>`
`Ctrl + ]` 跳转到光标下的关键词对应的标签文件，如果当前光标下没有标签，则光标右
侧的第一个关键词的

`Ctrl + T` 后退

`Ctrl + O` 前进

## tags

tags 设置了一个文件列表，列表中的每个文件都被用于搜索 tag，默认的 tag 文件即
tags
```vim
:set tags?
:set tags=./tags,tags
:set tags=./tags,tags,/home/user/commontags
```
