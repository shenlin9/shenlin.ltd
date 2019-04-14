---
title: Vim - 代码片段自动完成插件 UltiSnips
categories:
  - Vim
tags:
  - Vim
  - UltiSnips
---

Vim 的代码片段自动完成插件 UltiSnips

<!--more-->

## Snippet

Snippet 片段，指的是一小段经常重复使用的文本，或者包含了大量冗余文本的文本。

片段常用于结构化文本，如源码，但也可用于日常的编辑，如在邮件里插入签名或当前日
期等。

## UltiSnips

UltiSnips 就是用于 Vim 编辑器的片段管理插件，但只有设置为 `:set notcompatible` 时才可以正常使用。

UltiSnips 需要 Vim 启用 Python 支持，且 UltiSnips 会自动检测编译到 Vim 中的
Python 版本，有的 Vim 版本检测失败，则需设置全局变量：
```vim
let g:UltiSnipsUsePythonVersion = 2     " 2.x version python
let g:UltiSnipsUsePythonVersion = 3     " 3.x version python
```
