---
title: Vim 插件 Tabular
categories:
  - Vim
tags:
  - Vim
  - Tabular
---

Tabular 是一个 vim 对齐插件

<!--more-->

Tabular 的命令主要基于正则表达式。先使用正则表达式匹配字段分隔符，用匹配到的分隔
符分割输入行，从非分隔符的部分中删除不必要的空格，用空格填充行的非分隔符部分，使
它们保持相同的长度，并再次将它们连接在一起。

Tabular 可以设置正则表达式要匹配的范围，不指定范围时，默认范围就是当前行的连续上
下行（没有空行分隔）

设置 vim 不加载 tabular
```vim
:let g:tabular_loaded = 1
```

`:Tabularize` 可以缩写为 `:Tab`，但为避免冲突不建议映射或脚本里使用缩写，只建议
互动操作时使用

直接调用 `:Tabularize` 后面不加任何匹配模式时将自动调用最后一次的匹配模式

初始字符串

    Some short phrase,some other phrase
    A much longer phrase here,and another long phrase

默认的对齐：左对齐，字段之间使用一个空格分隔
            left-aligned with one space between fields
```vim
:Tabularize /,
```

    Some short phrase         , some other phrase
    A much longer phrase here , and another long phrase

改为右对齐，字段之间没有空格分隔
```vim
:Tabularize /,/r0
```

            Some short phrase,      some other phrase
    A much longer phrase here,and another long phrase

格式指示符：l,r,c 后面跟 1 位或多位数字

    l,r,c 分别表示左对齐，右对齐，中对齐
    数字表示插入在下一个字段前的空格数，所以设置最后一个字段空格数是无效的

可以指定一个格式指示符列表，则每个字段对应一个格式指示符，到列表结尾后再从头开始
对应，例如逗号前的右对齐，逗号后的左对齐
```vim
:Tabularize /,/r1c1l0
```

            Some short phrase , some other phrase
    A much longer phrase here , and another long phrase

注意不能写成 `/,/r1c0l1`，那样逗号后面就没有空格了
表示第一个字段右对齐，然后一个空格，逗号居中对齐，然后一个空格，接着第二个字段左
对齐，然后后面没有空格

当指示符列表被循环使用时，`l0` 的作用就体现出来了，例如：

    abc,def,ghi
    a,b
    a,b,c

```vim
:Tabularize /,/r1c1l0
```

    abc , def, ghi
      a , b
      a , b  ,  c

只对第一个逗号前的字段右对齐，然后后续所有内容作为整体左对齐
```vim
:Tabularize /^[^,]*\zs,/r0c0l0      ??? \zs 什么意思
```

    abc,def,ghi
      a,b
      a,b,c

可以把上面的命令参数起名保存，以后调用方便
```vim
:AddTabularPattern first_comma /^[^,]*\zs,/r0c0l0

:Tabularize first_comma
```

为使启动后上述命令即有效，可以创建一个 `.vim` 文件保存此类命令，并把此文件放入
`runtimepath` 定义的插件目录下，但为保证 `AddTabularPattern` 命令有效，必须让
`Tabular.vim` 优先启动，最简单的方法就是把创建的 `.vim` 文件要放入 `runtimepath`
定义的路径下的 `after/plugin` 目录，例如

    ~/.vim/after/plugin/tabular_extra.vim
    ~/.vim/after/plugin/tabular_maps.vim

位于 `$vim/vimfiles/tabular/after/plugin` 目录的 `TabularMaps.vim` 是默认的映射
文件

想使用多个空格作为分隔符分隔字段时，下面的方法都不可行
```vim
:Tabularize /  /
:Tabularize /  \+/
```
使用这种方法
```vim
:AddTabularPipeline multiple_spaces / \{2,}/
 \ map(a:lines, "substitute(v:val, ' \{2,}', '  ', 'g')")
 \   | tabular#TabularizeStrings(a:lines, '  ', 'l0')

:Tabularize multiple_spaces
```
