---
title: Vim - quickfix 和 location-list - vimgrep,quickfix,locationlist
categories:
- Vim
tags:
- Vim
- quickfix
- locationlist
- vimgrep
date: 2020-02-29 10:13:21
---

Vim - quickfix 和 location-list - vimgrep,quickfix,locationlist

<!--more-->

## quickfix & location-list

quickfix        把搜索匹配的结果填充到快速修复列表
location-list   把搜索匹配的结果填充到当前窗口的位置列表

两者主要区别：
quickfix 窗口是全局性的，每个 Session 只有一个，而 location-list 是按窗口分配的，几个窗口可以有几个。

命令：
```
:vim[grep][!] /{pattern}/[g][j] {file} ...
:vim[grep][!] {pattern} {file} ...
:vim[grep][!] {pattern} %
:vim[grep][!] {pattern} **

:lv[imgrep][!] /{pattern}/[g][j] {file} ...
:lv[imgrep][!] {pattern} {file} ...
```
* vim 表示填充到快速修复列表，lv 表示填充到当前窗口的位置列表
* ! 可紧随 vimgrep 之后，表示强制执行该命令
* 索引的关键字 pattern 放在了两个 “/” 中间，并且支持正则表达式
* g, j 可选。
* 如果添加 g，将显示重复行
* 如果添加 j，vim 将不会自动跳转到第一个匹配的行（可能是别的文件）
* file 可以是正则文件名，也可以是多个确定的文件名
* % 表示当前文件
* `**` 表示当前目录

quickfix 命令：
```vim
:vim pattern %  " 使用搜索结果填充 quickfix

:copen, :cw  " 打开 quickfix 窗口

:clist, :cl  " 使用 more 打开

:cnext, :cn  " 下一个结果

:cpre, :cp " 上一个结果

:cc         " 最后跳转的结果

:cclose, :ccl " 关闭 quickfix 窗口
```

location-list 命令，几乎是把 quickfix 命令的首字母 c 换为 l 即可：
```vim
:lv pattern %

:lnext, :lne

:lpre, :lp

:lopen, :lw

:lclose, :lcl
```

???还可以把错误写入当前的工作目录中一个文件，然后读入 quickfix 窗口
```vim
!g++ main.cpp 2>&1 |tee file1
cf fiel1
```

https://zhuanlan.zhihu.com/p/19632777
https://vim.fandom.com/wiki/Copy_search_matches
https://vim.fandom.com/wiki/Find_in_files_within_Vim
