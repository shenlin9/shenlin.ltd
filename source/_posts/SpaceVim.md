# SpaceVim

title: SpaceVim
categories:
  - Vim
  - SpaceVim
tags:
  - Vim
  - SpaceVim

---

https://github.com/SpaceVim/SpaceVim

## 依赖包

* vim 或 neovim
    * vim 需支持 python, python3 和 lua 特性
    * lua 特性是因为用到的插件 neocomplete 需要

* git 
    * SpaceVim 要通过 git 自动安装插件

* nerd fonts 和 true colors
    * SpaceVim 有 basic mode 和 awesome mode，使用后一种模式要安装 nerd fonts，终端也要支持 true colors

## 安装 SpaceVim

linux
```
curl -sLf https://spacevim.org/install.sh | bash
```

windows
```
//进入用户家目录
cd %HOME%

//原来的配置文件要改名禁用
mv _vimrc _vimrc_bak

//原来的 vimfiles 改名禁用
mv vimfiles vimfiles_bak

//克隆库文件到 vimfiles 目录
git clone https://github.com/SpaceVim/SpaceVim.git vimfiles

//如果是 neovim 则执行这句克隆
git clone https://github.com/SpaceVim/SpaceVim.git AppData\Local\nvim
```

## 自动安装插件

打开vim时会自动安装插件

自动执行了 cmd 命令
```
C:\Windows\system32\cmd.exe /c git clone https://github.com/Shougo/dein.vim "C:\
Users\ssl\.cache\vimfiles\repos\github.com\Shougo\dein.vim"

Cloning into 'C:\Users\ssl\.cache\vimfiles\repos\github.com\Shougo\dein.vim'...
remote: Counting objects: 4458, done.
remote: Total 4458 (delta 0), reused 0 (delta 0), pack-reused 4458
Receiving objects: 100% (4458/4458), 842.14 KiB | 159.00 KiB/s, done.
Resolving deltas: 100% (2564/2564), done.
Hit any key to close this window...
```

按任意键后接着又给出了 vim 提示框
```
:!git clone https://github.com/Shougo/dein.vim "C:\Users\ssl\.cache\vimfiles\repos\github.com\Shougo\dein.vim"
```

vim 打开文件后会自动执行 Updating plugins，第一次其实是安装插件
```
Updating plugins (153/154)
```

安装失败则手动安装
```
:call dein#install()
```
* dein 是 SpaceVim 的默认插件管理器

## Leader key

![](https://user-images.githubusercontent.com/13142418/27549426-8141bd86-5ace-11e7-9082-eb86b398acdc.png)

![](https://github.com/SpaceVim/SpaceVim/raw/dev/docs/img/unite_mappings.png)

## 配置文件

全局配置文件：`~/.SpaceVim.d/init.vim`

项目配置文件：`ProjectRoot/.SpaceVim.d/init.vim`

更改配置项语法：let itme_name = value

|配置项|说明|默认值|
|---|---|---|
|g:spacevim_error_symbol| Set the error symbol for SpaceVim's syntax maker |✖|
|g:spacevim_warning_symbol| Set the warning symbol for SpaceVim's syntax maker|⚠|
|g:spacevim_enable_debug|启用调试模式|0|
|g:spacevim_auto_disable_touchpad|插入模式自动禁用触摸板 ||
|g:spacevim_debug_level| debug 级别 |1|
|g:spacevim_buffer_index_type| 设定buffer索引类型，取值0,1,2  |0|
|g:spacevim_windows_index_type| 设定窗口索引类型，取值0,1,2  |0|
|g:spacevim_enable_tabline_filetype_icon|是否启用tabline的文件类型图标 |0|
|g:spacevim_enable_os_fileformat_icon|是否启用操作系统的文件类型图标 |0|
||||
|g:spacevim_default_indent|缩进默认值|2|
|g:spacevim_relativenumber|光标行上下显示相对数字|1|
|g:spacevim_max_column|列最大数|120|
|g:spacevim_enable_guicolors|终端是否支持true colors|1|
|g:spacevim_enable_googlesuggest|neocomplete插件是否提供google建议|0|
|g:spacevim_windows_leader| Window 函数的 leader 键，为空则表示禁用|s|
|g:spacevim_unite_leader| Unite 工作流的 leader 键，为空则表示禁用 |f|
|g:spacevim_denite_leader| Denite 工作流的 leader 键，为空则表示禁用 |F|
|g:spacevim_enable_insert_leader| 是否启用插入模式 leader 键|1|
|g:spacevim_plugin_bundle_dir| 插件的缓存目录 |`~/.cache/vimfiles`|
|g:spacevim_realtime_leader_guide| 是否启用实时的 leaer 键向导|1|
|g:spacevim_enable_key_frequency| Enable/Disable key frequency catching of SpaceVim.|0|
|g:spacevim_enable_neomake|是否启用 neomake 作为 checker，不启用则自动改用 syntastic|1|
|g:spacevim_guifont| 设置字体 |空|
|g:spacevim_enable_ycm| 是否启用 YouCompleteMe | 0 |
|g:spacevim_sidebar_width| 侧边栏的宽度，会被 tagbar 和 vimfiler 使用|30|
|g:spacevim_snippet_engine| 设定 snippet engine，选项：neosnippet,ultisnips|neosnippet|
|g:spacevim_statusline_separator| 设置状态栏的分割符，选项：1. arrow 2. curve 3. slant 4. nil 5. fire |arrow|
|g:spacevim_enable_cursorline|是否高亮光标行 |1| 
|g:spacevim_enable_cursorcolumn|是否高亮光标列 |0| 
|g:spacevim_terminal_cursor_shape|设置终端里的光标样式: 0 表示禁止nvim改变样式 1 表示不闪烁对模式敏感 2 表示闪烁对模式敏感 |1|
|g:spacevim_vim_help_language| 设定vim帮助的语言， let g:spacevim_vim_help_language = 'chinese'|en|
|g:spacevim_language| 设定vim提示信息的语言 |en_US.UTF-8|
|g:spacevim_colorscheme|默认的 colorscheme|gruvbox|
|g:spacevim_colorscheme_bg|colorscheme 的背景色|dark|
|g:spacevim_colorscheme_default|如果 g:spacevim_colorscheme 设定的 colorscheme 没安装时的默认 colorscheme |desert|
|g:spacevim_github_username| github 用户名，用来获取用户收藏的库，和模糊查找库||
|g:spacevim_force_global_config| 有项目配置文件时，全局配置文件则不加载，设为 1 则在加载项目配置文件后再加载全局配置文件 |0|
|g:spacevim_enable_powerline_fonts| Enable/Disable powerline symbols. Default is 1.|1|
|g:spacevim_lint_on_save| Enable/Disable lint on save feature of SpaceVim's maker.|1|
|g:spacevim_lint_on_the_fly| Enable/Disable lint on the fly feature of SpaceVim's maker.|0|
|g:spacevim_enable_vimfiler_welcome| 在欢迎窗口显示 vimfiler ，文件过多时会使得vim启动变慢|1|
|g:spacevim_enable_vimfiler_gitstatus| 在vimfiler 和 buffer 中显示 git 状态列|0|
|g:spacevim_enable_vimfiler_filetypeicon| 在vimfiler 和 buffer 中显示文件类型图标列|0|
|g:spacevim_hosts_url| host 文件 url， 是为了不能使用 Google 和 Twitter 的国内用户||
|g:spacevim_filemanager|默认的文件管理器|vimfiler| 
|g:spacevim_plugin_manager|默认的插件管理器，选项包括：dein, neobundle, vim-plug|dein|
|g:spacevim_plugin_manager_max_processes| 插件管理器的最大进程数||
|g:spacevim_checkinstall|启动时检测安装|1|
|g:spacevim_simple_mode| 是否启用简单模式，这种模式下只安装少数插件|0|
|g:spacevim_plugin_groups| 要加载的插件群  let g:spacevim_plugin_groups = ['core', 'lang']||
|g:spacevim_disabled_plugins| 通过名字禁用插件 let g:spacevim_disabled_plugins = ['vim-foo', 'vim-bar']||
|g:spacevim_custom_plugins| 添加自定义插件||
```
  let g:spacevim_custom_plugins = [
              \ ['plasticboy/vim-markdown', 'on_ft' : 'markdown'],
              \ ['wsdjeg/GitHub.vim'],
              \ ]
```

## 命令

区分大小写

加载已有的 layer，可为 layer 的名字字符串或列表
```
:SPLayer {layers}
```

显示版本号，+ - 号分别表示是否启用了某个特性
```
:SPVersion
```

获取或设置选项值
```
:SPSet {opt} [value]
```
* `{opt}` 为上面的配置项去掉前面的`g:spacevim_`

输出 debug 信息
```
:SPDebugInfo[!]
```
* [!] 强制输出到新的 buffer

编辑配置文件
```
:SPConfig
```
* -g 选项或者默认都是编辑全局配置文件
* -l 选项表示项目配置文件

更新插件
```
:SPUpdate
```

安装插件
```
:SPInstall
```

## 方法

加载 layer
```
SpaceVim#layers#load({layer})
```

设置 debug 级别，默认是1
```
SpaceVim#logger#setLevel({level})
```

  1 : 所有记录

  2 : 警告和错误记录

  3 : 只有错误记录

设置日志输出文件
```
SpaceVim#logger#setOutput({file})
```
* 默认文件 `~/.SpaceVim/.SpaceVim.log`.

## Layer

    default         better default for vim and neovim
    checkers        checking in vim
    autocomplete    autocomplete in vim
    chinese 	    layer for chinese vimer
    colorscheme     all colorscheme in spacevim
    chat            chatting in vim
    lang#java       java development in vim
    lang#lisp       lisp development in vim
    lang#markdown   layer for editing markdown in vim
    lang#php        php development in vim

### default

SpaceVim default layer contains none plugins, but it has some better default config for vim and neovim.

### checkers

This layer provides syntax checking feature.
checkers layer is loaded by default.

g:spacevim_enable_neomake 	1 	Use neomake as default checking tools, to use syntastic, set this option to 0.
g:spacevim_lint_on_the_fly 	0 	Syntax checking on the fly feature, disabled by default.

### colorscheme

This layer provides many Vim colorschemes for SpaceVim, the default colorscheme is gruvbox.

Some colorschemes offer dark and light styles. Most of them are set by changing Vim background color. 

### autocomplete

SpaceVim uses neocomplete as the default completion engine if vim has lua support. If there is no lua support, neocomplcache will be used for the completion engine. Spacevim uses deoplete as the default completion engine for neovim. Deoplete requires neovim to be compiled with python support. For more information about python support in neovim, please read neovim’s documentation :h provider-python.

SpaceVim use neosnippet as the default snippet engine. The default snippets are provided by Shougo/neosnippet-snippets. For more information, please read :h neosnippet. Neosnippet support custom snippets, and the default snippets directory is ~/.SpaceVim/snippets/. If g:spacevim_force_global_config = 1, SpaceVim will not append ./.SpaceVim/snippets as default snippets directory.

### lang#markdown

Key 	    mode 	description
SPC l ft 	Normal 	Format table under cursor
SPC l p 	Normal 	Real-time markdown preview

### lang#php

需安装:`SPLayer 'lang#php'`

Requirement

    PHP 5.3+
    PCNTL Extension
    Msgpack 0.5.7+(for NeoVim) Extension or JSON(for Vim 7.4+) Extension
    Composer Project


### chat

安装的是 vim-chat 插件

Alt + x : open chatting buffer for qq. Alt + w : open chatting buffer for weixin.

within chatting buffer:

Alt + Left/Right : switch between buffer. Alt + 1-9 : jump to specified channel.

for more mappings in chatting buffer, please read :h vim-chat.

## 编译 vimproc

SpaceVim 启动时总是有下列提示：

```
[vimproc]vimproc's DLL:"C:/users/ssl/.cache/vimfiles/repos/github.com/Shougo...m/lib/vimproc_win32.dll" is not found.
Please read :help vimproc and make it.
```

需要自己编译生成 vimproc_win32.dll

先使用 Mingw 安装 gcc 编译环境，具体步骤参见文章：[Windows 下使用 MinGW 安装 gcc 编译环境](Windows 下使用 MinGW 安装 gcc 编译环境.md)

下载 vimproc 库，SpaceVim 已下载，省略
```
//进入 vimproc 库目录
cd C:\Users\ssl\.cache\vimfiles\repos\github.com\Shougo\vimproc.vim\src

//编译
λ mingw32-make -f make_mingw32.mak

    提示丢失 libmingwex-0.dll,有几乎同名的 libmingwex.a，但不管用，搜索发现真有 libmingwex 这个包

//安装这个库
λ mingw-get install libmingwex

//再次编译成功
λ mingw32-make -f make_mingw32.mak

//复制文件到 SpaceVim 的 runtimepath
λ cp auload/* plugin/* lib/* $HOME/.SpaceVim.d/
```

## Q&amp;A
