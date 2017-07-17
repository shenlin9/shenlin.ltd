# vim 安装插件管理器 vundle
标签: AAA vim vundle
title: vim 安装插件管理器 vundle
categories:
  - vim
tags:
  - vim
  - vundle
---

vundle 表示 vim bundle，是一个 vim 的插件管理器

vundle 依赖于 git 和 curl，需要先安装它们，并且把他们的路径加入环境变量 PATH
```
C:\Program Files\Git\bin;C:\Program Files\Git\mingw32\bin;C:\Program Files\Git\mingw32\libexec\git-core;
```

使用 git 把 vundle 库克隆到目标文件夹
```
//windows 安装
cd %USERPROFILE%
git clone https://github.com/VundleVim/Vundle.vim.git %USERPROFILE%/.vim/bundle/Vundle.vim

//linux 安装
$ git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

打开配置文件
````
//windows
gvim $VIM\_vimrc

//linux
vim ~/.vimrc
```

将下列内容写入配置文件
```
set nocompatible
filetype off

//把 vundle 路径写入 vim 的 runtimepath
set rtp+=~/.vim/bundle/Vundle.vim

call vundle#begin()

//安装 vundle 本身
Plugin 'VundleVim/Vundle.vim'

//安装的一个 markdown 插件
Plugin 'iamcco/mathjax-support-for-mkdp'
Plugin 'iamcco/markdown-preview.vim'

call vundle#end()
filetype plugin indent on
```
* 所有插件必须写在`call vundle#begin()`和`call vundle#end()`之间
* 安装 github 的插件直接写"作者/库名"即可
* %USERPROFILE% 值为 c:\users\ssl
* $VIM 为 vim 的安装根目录

重新载入配置文件，查看插件列表
```
:so $VIM\_vimrc

:PluginList
```

安装插件
```
:PluginInstall
```

安装配色方案
```
Plugin 'desert-warm-256'
colorscheme desert-warm
```
https://github.com/VundleVim/Vundle.vim/issues/119