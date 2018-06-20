---
title: Vim - 自动换行 textwidth, formatoptions, fo-table, colorcolumn, wrapmargin
categories: Vim
tags: Vim
---

VIM 几个换行相关的设置

<!--more-->

## 快捷键

shift + v, shift + q，即先大写的 V 选中当前行，然后大写的 Q 自动换行

## textwidth

设置文本行的最大字符数，超过即自动换行
0 表示不换行
对于已存在的行，先 shift + v 选中行，然后按 gq 则进行自动换行
```vim
set tw=80
set textwidth=80
```

## formatoptions

formatoptions 不是只用于换行的选项，而是一系列标记的列表，表示 vim 怎样格式化文
本，如在注释里直接回车会自动在新行插入注释头就是使用的 r 标记
```vim
"根据 textwidth 的设置自动换行，默认是使用空格分隔的英文单词
set fo+=t

"对于编码在 255 以上的多字节字符使用换行，如亚洲文字每个字都是
"一个独立的词
set fo+=m

"当把两行合并为一行时，对于连接的两个多字节字符之间不插入空格
set fo+=M
```
* 不更改原标记，在原有标记的基础上直接添加新标记使用上面的语法
* 注意只能一次添加一个标记，不能合写为 fo+=tmM
* 默认标记是：qlnr
    * q 允许使用 gq 格式化
    * l 插入模式下在超过 textwidth 后也不自动换行
    * n 格式化文本时自动识别列表编号，如 1. 1: 1)
    * r 在注释里直接回车会自动在新行插入注释头
* 更多标记参考 help fo-table

## wrapmargin

wrapmargin 只在 textwidth=0 时有效，表示在距离窗口右边界几个字符时自动插入换行符
换行，默认值是 0，即不自动换行
```vim
set wrapmargin=0
set wm=0
```

## colorcolumn

高亮第80列
```vim
set cc=80
set colorcolumn=80
```

高亮 'textwidth' 后的第1列
```vim
set cc=+1
```

高亮 'textwidth' 后的第1，2，3列
```vim
set cc=+1,+2,+3
```

设置高亮列的样式
```vim
hi ColorColumn ctermbg=lightgrey guibg=lightgrey
```
