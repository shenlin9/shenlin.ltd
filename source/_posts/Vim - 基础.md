---
title: Vim 基础
categories: Vim
tags: Vim
---

vim 的基本设置和基础操作

<!--more-->

## 快捷键

http://www.jianshu.com/p/8ae25a680ed7

## 寄存器

http://www.cnblogs.com/bwangel23/p/4421957.html

显示寄存器列表
```vim
:reg
```

粘贴寄存器内容
```vim
"<n>p
```

## 跳转

http://easwy.com/blog/archives/advanced-vim-skills-advanced-move-method/

ma 设定标记a
'a 跳转到设定的标记a

'' 在最后两次的位置来回跳转

zt
zz
zb

## vim 环境变量

查看环境变量
```vim
:echo $HOME
C:\Users\ssl

:echo $VIM
C:\Program Files\Vim

:echo $VIMRUNTIME
C:\Program Files\Vim\vim80
```

查看路径
```vim
:set runtimepath
:set rtp
```

设置路径
```vim
:set rtp+=~/.vim/bundle/Vundle.vim
:set rtp+=$ProgramFiles\Git\bin
:set rtp+=$ProgramFiles\Git\mingw32\bin
:set rtp+=$ProgramFiles\Git\mingw32\libexec\git-core
```

重新加载配置文件
```vim
//windows
:source $VIM\_vimrc
:so $VIM\_vimrc

//linux
:source ~\.vimrc
:so ~\.vimrc
```

? MyDiff 已存在，要使用 ! 覆盖
? source `$VIMRUNTIME/vimrc_example.vim` 作用
? Pathogen 和 Vundle

## python 环境

vim 里执行命令输出如下说明支持 python 特性
```vim
:version
...+python/dyn +python3/dyn..
```

vim 里执行命令输出如下说明已安装 python 环境
```vim
:echo has("python")
1

:echo has("python3")
1
```
* 0 表示未安装 python 环境
* 还发现个问题就是桌面上的文件使用 vim 打开，执行上述命令时返回0，其他目录则返回1
* 未安装 python 环境，要先安装 python 再安装 vim
* python 不要安装 3.6 版本，vim 不支持，安装 3.5 版本即开

lua 等的检测是一样的

## 查找和替换

http://blog.csdn.net/zcube/article/details/42710141

## 正则表达式

http://www.cnblogs.com/RigorosLee/archive/2011/05/13/2045806.html

|元字符|说明|
|---|---|
|.|匹配任意一个字符|
|[abc]|匹配方括号中的任意一个字符。可以使用-表示字符范围， 如[a-z0-9]匹配小写字母和阿拉伯数字。|
|[^abc]|在方括号内开头使用^符号，表示匹配除方括号中字符之外的任意字符。|
|\d|匹配阿拉伯数字，等同于[0-9]。|
|\D|匹配阿拉伯数字之外的任意字符，等同于[^0-9]。|
|\x|匹配十六进制数字，等同于[0-9A-Fa-f]。|
|\X|匹配十六进制数字，等同于[^0-9A-Fa-f]。|
|\w|匹配单词字母，等同于[0-9A-Za-z_]。|
|\W|匹配单词字母之外的任意字符，等同于[^0-9A-Za-z_]。|
|\t|匹配<TAB>字符。|
|\s|匹配空白字符，等同于[ \t]。|
|\S|匹配非空白字符，等同于[^ \t]。|

## 转换大小写

gu 转换为小写
gU 转换为大写

整篇文章

    ggguG
    gggUG

单词转换

    guw 、gue
    gUw、gUe
    gu5w、gu5e

行转换

    gU0        ：从光标所在位置到行首，都变为大写
    gU$        ：从光标所在位置到行尾，都变为大写
    gUG        ：从光标所在位置到文章最后一个字符，都变为大写

## 文件最后一行结束符

EOL 表示 endofline

vim 认为文件是由行组成的，并且文件最后一行是以`<EOL>`为结束符的

查看当前设置
```vim
:echo &eol
```

设置文件以`<EOL>`结尾
```vim
:set eol
```

不以`<EOL>`结尾
```vim
:set noeol
```

查看是否自动修正不以`<EOL>`结尾的文件
```vim
:echo &fixeol
```

自动修正
```vim
:set fixeol
```

不自动修正
```vim
:set nofixeol
```

## 回车和换行

windows, linux, macos 对待换行的方式都不一样，需要进行针对性设置

### 读写文件时

设置读取文件时的换行检测
```vim
:set fileformats=dos,unix
:set ffs=dos,unix
```

查看、设置当前文件换行格式
```vim
:set fileformat
:set fileformat=unix
:set ff=unix
```

    取值和对应的换行符
    dos	    <CR> <NL>
    unix    <NL>
    mac	    <CR>

显示换行符和 tab 符
```vim
:set list
```

取消换行符和 tab 符的显示
```vim
:set nolist
```

### 查找替换时

查找时，\n 代表换行符 (newline)， \r 代表回车符 (CR 即 carriage return = Ctrl-M = ^M)，若查找 \r 没有找到，则可能是 linux 换行格式文件

替换时，\r 代表换行，\n 代表空字符 (null byte 0x00)

那替换时怎么替换掉回车符呢？ 下列命令可以转换DOS回车符“^M”为真正的换行符：

```vim
:%s/\r/\r/g
```

### FileFormat 和 文件中的 ^M 字符

设置文件的换行格式是 unix 还是 dos，

unix 是以一个换行符作为换行，dos 则表示以回车符和换行符两个符号一起来表示换行

```vim
"查询当前文件的换行格式
:set ff?

"设置当前文件的换行格式
:set ff=dos
:set ff=unix

"设置读取文件时的换行检测
:set ffs=unix,dos
```

有时在文本编辑器里看到的的 `^M` 字符，

是因为 windows 以回车 `\r` 和换行 `\n` 两个字符表示换行，

linux 以 `\n` 表示换行，`^M` 就是 linux 下多余出来的回车 `\r` 字符

单文件替换 `^M` 字符为空字符可以使用下面命令

```vim
:%s///g
```
* 注意上面命令中的 ^ 和 M 字符不是直接输入的，而是按住 Ctrl+v Ctrl+m 产生的

多文件替换可以使用 dos2unix 命令

```bash
$ find ./ -type f -print0 | xargs -0 dos2unix
```

### BOM

byte of mark 字节序

```vim
:set nobomb
```

## buffer操作

    :ls     查看当前已打开的buffer
    :b n    切换buffer，n为buffer list中的编号
    :bn     下一个 buffer
    :bp     上一个 buffer
    :b#     前一个 buffer
    :bdelete n 删除buffer，n为buffer list中的编号

## 光标跳转

    ctrl+o 
    ctrl+i

## colorscheme

查看当前scheme

    :colorscheme

查看已安装的scheme

    :colorscheme <Tab>

* 注意有个空格和 Tab
* 可以通过按 Tab 在 scheme 之间切换

恢复上次文件打开位置
```vim
set viminfo='10,\"100,:20,%,n~/.viminfo
au BufReadPost * if line("'\"") > 0|if line("'\"") <= line("$")|exe("norm '\"")|else|exe "norm $"|endif|endif
```

文件被外部应用改动时，自动读入文件：
```vim
if exists("&autoread")
    set autoread
endif
```

30s 自动保存文件
```vim
let autosave=30
```

cd 和 lcd 命令区别

> cd 意思为 global current directory
> lcd 意思为 local current directory

> cd 可以指定全局的目录，这样方便使用 :edit , :Explore 来查找编辑文件。
> 但在多窗口和多 tab 时需要去其他目录查阅资料，这时使用全局目录切来切去很麻烦，可使用 lcd 对当前窗口指定一个工作目录。

> 查看当前目录使用 :pwd 命令

## 关于 Tab

显示 Tab 字符
```vim
:set list
```
* 默认 Tab 将显示为 `^I`
* 行尾显示为 `$`
* 没显示行首

将 Tab 字符转换为 `>_`
```vim
:set list listchars=tab:>_
```

Tab、空格和缩进
```vim
/\t
:retab
:set expandtab
:set tabstop=4
:set softtabstop=4
:set shiftwidth=4
```
* 搜索时使用 \t 表示 Tab
* retab

    将现有 Tab 全部转换为空格

* expandtab

    输入的 tab 键都转换为空格

* tabstop

    vim 读取文件遇到 \t 字符时，解释为几个空格长度

* softtabstop

    编辑文件时，将几个空格作为一个 tab 处理，

    针对的是 `<Backspace>` 和 `<Tab>` 按键，如设为4，

    则按`<Tab>`时插入4个空格宽度，按`<Backspace>`时把

    4个空格作为一个tab删除

* shifwidth

    缩进时使用几个空格


## 拆分窗口

    快捷键按键方法：先一起按下Ctrl + W，然后释放，再按对应的第三键，不是三个键一起按
    
    1、水平窗口分割：
        $ vim  -o  file1 file2  水平分割成两个窗口分别显示文件
        :split         （开启另一个窗口察看同一文件）
        :split 文件名  （开启另一个窗口察看指定文件）

    2、垂直窗口分割：
        $ vim  -O  file1 file2  垂直分割成两个窗口分别显示文件
        :vsplit（开启另一个窗口察看同一文件）
        :vsplit 文件名（开启另一个窗口察看指定文件）

    3、在窗口之间进行切换：
        Ctrl + w + w： 在所有窗口中循环移动
        Ctrl + w + h： 向左移动窗口
        Ctrl + w + j： 向下移动窗口
        Ctrl + w + k： 向上移动窗口
        Ctrl + w + l： 向右移动窗口

    4、增大或减少窗口大小：
        Ctrl + W + =：让所有窗口调整至相同尺寸（平均划分）
        Ctrl + W + -：将当前窗口的高度减少一行，- 号前也可以加数字指定行数，也可在ex命令中，：resize -4明确指定减少的尺寸
        Ctrl + W + +：将当前窗口的高度增加一行，+ 号前也可以加数字指定行数。同样在ex命令中，：resize +n 明确指定增加尺寸

        Ctrl + W + < ：将当前窗口的宽度减少
        Ctrl + W + > ：将当前窗口的宽度增加
        Ctrl + W + |：将当前窗口的宽度调到最大，也可他哦你通过ex命令：vertical resize n明确指定改变宽度

    5、关闭窗口：
        四种方法：离开（quit）、关闭（close）、隐藏（hide）、关闭其他窗口（only）

        1）将光标切换到当前窗口下，然后按照关闭单个窗口的方法关闭窗口。例如：q命令。
        2）关闭所有窗口文件：在所有关闭单个窗口的命令中加上all，例如：qall命令。
        3）关闭除当前窗口之外的文件：only。

## 帮助文件导航

    移动光标到某个标签，然后 Ctrl + ] 跳转到主题，Ctrl + O 返回

## package packpath

vim 中的 package 就是一个包含一个或多个 plugin 的目录

package 比 plugin 的优点：

* package 可以作为一个归档整体下载和解档到它自己的目录，文件不会和其它插件文件混在一起，也因此更新和删除更方便
* package 可以是一个 git, mercurial 等一类的库，更新方便
* 一个 package 可以包含多个互相依赖的 plugin
* package 中的插件可以在 vim 启动时自动加载或在需要时使用`:packadd`命令加载


vim 启动时，处理完配置文件`.vimrc`后，会接着扫描`packpath`中所有路径的子目录`pack/*/start`来查找 plugin，首先把所有`pack/*/start`路径添加到`runtimepath`，然后加载所有找到的plugin，`pack/*/opt`不会被添加到 runtimepath。

`pack/*/start/lib/autoload` 文件夹常用来放多个插件共用的文件，如


	pack/foo/start/one/plugin/one.vim
		call foolib#getit()
	pack/foo/start/two/plugin/two.vim
		call foolib#getit()

	pack/foo/start/lib/autoload/foolib.vim
		func foolib#getit()

如果 package 包含一个名为 after 的目录，则会被放到 `runtimepath` 的后面，则 `after` 目录里的 plugin 将会加载的较晚。


如果不是一个 package，而是一个独立的 plugin，则需要自己创建 start 目录
```vim
% mkdir -p ~/.vim/pack/foo/start/foobar
% cd ~/.vim/pack/foo/start/foobar
% unzip /tmp/someplugin.zip
```
目录结构为

	pack/foo/start/foobar/plugin/foo.vim
	pack/foo/start/foobar/syntax/some.vim

对比 package 则只需创建包目录, 包名 foo 是随意的：
```vim
	% mkdir -p ~/.vim/pack/foo
	% cd ~/.vim/pack/foo
	% unzip /tmp/foopack.zip
```
目录结构为

	pack/foo/README.txt
	pack/foo/start/foobar/plugin/foo.vim
	pack/foo/start/foobar/syntax/some.vim
	pack/foo/opt/foodebug/plugin/debugger.vim


操作命令
```vim
"$HOME,$VIM 等没有展开
:set packpath
:set pp

"全展开了
:echo &pp

"
set packpath+=~/.vim


"加载所有包命令
:packloadall
```

## runtimepath


runtimepath 是一个目录列表，用来定位运行时文件

    filetype.vim	filetypes by file name |new-filetype|
    scripts.vim	filetypes by file contents |new-filetype-scripts|
    autoload/	automatically loaded scripts |autoload-functions|
    colors/	color scheme files |:colorscheme|
    compiler/	compiler files |:compiler|
    doc/		documentation |write-local-help|
    ftplugin/	filetype plugins |write-filetype-plugin|
    indent/	indent scripts |indent-expression|
    keymap/	key mapping files |mbyte-keymap|
    lang/		menu translations |:menutrans|
    menu.vim	GUI menus |menu.vim|
    pack/		packages |:packadd|
    plugin/	plugin scripts |write-plugin|
    print/	files for printing |postscript-print-encoding|
    spell/	spell checking files |spell|
    syntax/	syntax files |mysyntaxfile|
    tutor/	files for vimtutor |tutor|


大多数的系统默认设置搜索5个地方：

	1. In your home directory, for your personal preferences.
	2. In a system-wide Vim directory, for preferences from the system administrator.
	3. In $VIMRUNTIME, for files distributed with Vim.  *after-directory*
	4. In the "after" directory in the system-wide Vim directory.  This is
	   for the system administrator to overrule or add to the distributed
	   defaults (rarely needed)
	5. In the "after" directory in your home directory.  This is for
	   personal preferences to overrule or add to the distributed defaults
	   or system-wide settings (rarely needed).


操作命令
```vim
"这样的查询 $HOME,$VIM 没有展开为完整目录
:set runtimepath
:set rtp

"这样则全部展开了
:echo &rtp

"增加runtimepath 路径
:set runtimepath+=some\path,some\path
:runtime syntax/c.vim
:ru syntax/c.vim
```

## .netrwhist

netrw 是一个通过网络读写文件的 vim 插件，.netrwhist 就是 netrw 的历史记录文件，保存的是被修改的目录的列表，当修改 ~/.vim 目录下的文件时就加入记录

内容一般如下：
```vim
let g:netrw_dirhistmax  =10
let g:netrw_dirhist_cnt =6
let g:netrw_dirhist_1='/Users/wolever/EnSi/repos/web/env/web/lib/python2.6/site-packages/django'
let g:netrw_dirhist_2='/private/tmp/b/.hg/attic'
let g:netrw_dirhist_3='/Users/wolever/code/sandbox/pydhcplib-0.6.2/pydhcplib'
let g:netrw_dirhist_4='/Users/wolever/EnSi/repos/common/env/common/bin'
let g:netrw_dirhist_5='/Users/wolever/EnSi/repos/common/explode'
let g:netrw_dirhist_6='/Users/wolever/Sites/massuni-wiki/conf'
```
* netrw_dirhistmax 表示记录的最多历史条数
* netrw_dirhist_cnt 表示当前已记录条数

没有禁用产生 .netrwhist 文件的方法，可以设置为产生后立即自动删除
```vim
au VimLeave * if filereadable("[path here]/.netrwhist")|call delete("[path here]/.netrwhist")|endif 
```

或设置最多记录数为 0 则不再记录，但原先的 .netrwhist 或 .netrwbook 并不会自动删除 
```vim
:let g:netrw_dirhistmax = 0
```

可以指定 .netrwhist 文件的位置，以免和 vim 的 runtimepath 文件混在一起
```vim
let g:netrw_home=$XDG_CACHE_HOME.'/vim'
```

## vim 查看文件时多了很多 <200b> <200c> <200d>

如：
`git submodule [--quiet] deinit [-f|--force] (--all|[--] <path>…​)`

commonly abbreviated ZWSP
this character is intended for invisible word separation and for line break control; it has no width, but its presence between two characters does not prevent increased letter spacing in justification
http://www.fileformat.info/info/unicode/char/200B/index.htm

这些字符是排版过程中产生的，而排版使用的规范是Unicode编码标准

|name|unicode code point|utf-8(in literal)|
|---|---|---|
|ZERO WIDTH SPACE|U+200b|\xe2\x80\x8b|
|ZERO WIDTH JOINER|U+200c|\xe2\x80\x8c|
|ZERO WIDTH NON-JOINER|U+200d|\xe2\x80\x8d|

```vim
$value = str_replace("\xe2\x80\x8b", '', $value); 
$value = str_replace("\xe2\x80\x8c", '', $value); 
$value = str_replace("\xe2\x80\x8d", '', $value); 
```

https://superuser.com/questions/207207/how-can-i-delete-u200b-zero-width-space-using-sed

## Q&amp;A

### 提示 “modifiable is off”

modifiable 关闭时不允许更改缓存内容，也不允许更改 `fileformat` 和 `fileencoding`
```vim
:set modifiable
:set nomodifiable

//可简写为
:set ma
:set noma
```

出现上述提示可能是插件或者设置错误导致的，可运行下面命令尝试找到错误原因：
```vim
:verbose setlocal modifiable?
```

使用下列命令打开
```vim
:autocmd BufWinEnter * setlocal modifiable
```
