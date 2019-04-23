---
title: Vim - 模糊搜索插件 Ctrlp
categories:
  - Vim
tags:
  - Vim
  - Ctrlp
---

Vim - 模糊搜索插件 Ctrlp

<!--more-->

## 基本命令和选项

设置 CtrlP 的触发快捷键和默认查找方式
```vim
let g:ctrlp_map = '<c-p>'
let g:ctrlp_cmd = 'CtrlP'
```

查找模式包括：
* `:CtrlP` 或 `:CtrlP [starting-directory]` 调用 CtrlP 查找文件列表
* `:CtrlPBuffer` 调用 CtrlP 查找缓存
* `:CtrlPMRU` 调用 CtrlP 查找最近使用文件
  * MRU : Most Recently Used 
* `:CtrlPMixed` 调用 CtrlP 同时查找最近使用文件、缓存、文件列表

## 基本快捷键

`<F5>` to purge the cache for the current directory to get new files, remove deleted files and apply new ignore options.
`<c-b>` to cycle between modes.
`<c-d>` to switch to filename search instead of full path.
`<c-r>` to switch to regexp mode.
`<c-j>`, `<c-k>` or the arrow keys to navigate the result list.
`<c-t>` or `<c-v>`, `<c-x>` to open the selected entry in a new tab or in a new split.
`<c-n>,` `<c-p>` to select the next/previous string in the prompt's history.
`<c-y>` to create a new file and its parent directories.
`<c-z>` to mark/unmark multiple files and `<c-o>` to open them.
