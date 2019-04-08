---
title: Vim - 对齐插件 Tabular
categories:
  - Vim
tags:
  - Vim
  - Tabular
---

Vim 对齐插件 Tabular

<!--more-->

## How It Works？

Tabular 的命令主要基于正则表达式。

Tabular 使用正则表达式来匹配字段分隔符，使用分隔符分割输入行，对被分隔的部分或者删除不必要的空格，或者用空格填充，总之使它们的长度相同，然后再将它们重新连接起来。

## The Syntax & Examples

例如下列文本要以逗号对齐：

    Some short phrase,some other phrase
    A much longer phrase here,and another long phrase

则将光标定位到上面两行之一，再传递给 Tabular 匹配逗号的正则：

```vim
:Tabularize /,
```

执行结果

    Some short phrase         , some other phrase
    A much longer phrase here , and another long phrase

Tabular 默认的执行范围为光标处连续的行，这个范围也是可以更改的。

只执行 `:Tabularize` 命令而不给出正则表达式，则表示使用上次的正则。

Tabular 默认左对齐，且被分隔部分之间使用一个空格，见上面的文本，逗号左右各一
个空格，若要右对齐且被分隔部分之间不使用空格，则：
```vim
:Tabularize /,/r0
```

            Some short phrase,      some other phrase
    A much longer phrase here,and another long phrase

右对齐使用一个空格
```vim
:Tabularize /,/r1
```

            Some short phrase ,       some other phrase
    A much longer phrase here , and another long phrase

注意 `r` 后面的数字是必须的，否则不会有任何效果。

## Format Specifier

上面的 `r0`, `r1` 称为格式说明符（Format Specifier），由一个字母和后面的数字组
成，字母可以取值 `l`, `r`, `c`，分别表示左对齐，右对齐和中间对齐，后面的数字表
示当前分隔部分后面填充的空格数量。

多个格式说明符可以用于同一个命令，表示对分隔部分依次循环使用格式说明符，即使用完毕最后一个格式说明符后，还有分隔部分，则对下一个分隔部分使用第一个格式说明符。

逗号前面的部分左对齐，逗号后面的部分右对齐：
```
:Tabularize /,/r1c1l0
```

            Some short phrase , some other phrase
    A much longer phrase here , and another long phrase

虽然上面格式说明符中的 `l0` 貌似没起作用，但若被分隔部分足够长需要循环时则可看出
效果：

    abc,def,ghi
    a,b
    a,b,c

```
:Tabularize /,/r1c1l0
```

    abc , def, ghi
      a , b
      a , b  ,  c

```
:Tabularize /,/r1c1l1
```

    abc , def , ghi
      a , b
      a , b   ,  c

## Name

若只要对第一个逗号执行命令，而不考虑后面的逗号，例如第一个逗号前的内容右对齐，逗
号中对齐，后面的全部内容左对齐，则：
```
:Tabularize /^[^,]*\zs,/r0c0l0
```

    abc,def,ghi
      a,b
      a,b,c

看起来复杂的命令，其实只是使用了 Vim 的正则，来匹配第一个逗号。[Vim 正则参考](http://vimdoc.sourceforge.net/htmldoc/pattern.html)

为了更方面使用，可以给上面的复杂命令起个名字：
```
:AddTabularPattern first_comma /^[^,]*\zs,/r0c0l0
```

则再执行命令有相同效果：
```
:Tabularize first_comma
```

为使 Vim 启动后上述命令即有效，可以创建一个 `.vim` 文件保存此类命令，并把此文件放入`runtimepath` 定义的插件目录下。

但为保证 `AddTabularPattern` 命令有效，必须让 `Tabular.vim` 优先启动，最简单的方法就是把创建的 `.vim` 文件要放入 `runtimepath` 定义的路径下的 `after/plugin` 目录，例如

    ~/.vim/bundle/tabular/after/plugin/TabularExtra.vim

位于 `~/.vim/bundle/tabular/after/plugin/TabularMaps.vim` 目录的 `TabularMaps.vim` 是默认的映射文件，可以把自己创建的扩展放入此目录下。

另外需要注意的是，自己创建的扩展文件中要检测 `Tabular.vim` 确实被载入了：
```vim
" after/plugin/my_tabular_commands.vim
" Provides extra :Tabularize commands

if !exists(':Tabularize')
  finish " Give up here; the Tabular plugin musn't have been loaded
endif

" Make line wrapping possible by resetting the 'cpo' option, first saving it
let s:save_cpo = &cpo
set cpo&vim

AddTabularPattern! asterisk /*/l1

AddTabularPipeline! remove_leading_spaces /^ /
                \ map(a:lines, "substitute(v:val, '^ *', '', '')")

" Restore the saved value of 'cpo'
let &cpo = s:save_cpo
unlet s:save_cpo
```

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

设置 Vim 不加载 Tabular
```vim
:let g:tabular_loaded = 1
```

设置 Vim 不加载 `TabularMaps.vim` 中的默认命令 
```vim
:let g:no_default_tabular_maps = 0
```

