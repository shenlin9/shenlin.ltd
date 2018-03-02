---
title: Vim - eol, fixeol, binary
categories: Vim
tags: Vim
---

eol, end of line, 在 vim 中用 <EOL> 表示

<!--more-->

## <EOL>

end-of-line 可能是 `<CR>`, `<LF>` 或者 `<CR><LF>`,

具体的换行符由系统或`fileformat`决定

## eol

设置是否在文件的最后一行添加换行符

    set eol
    set noeol

默认开启

## fixeol

设置是否在文件最后一行缺少换行符时添加上

默认开启

## binary

默认关闭
编辑二进制文件应该开启

当 binary 关闭时，<EOL> 具体的换行符由 fileformat 决定
当 binary 开启时，有关 eol 的相关设置无效，如 fixeol

	set bin
	set nobin
