---
title: NeoVim NyaoVim
categories:
  - Vim
tags:
  - Vim
  - NeoVim
  - NyaoVim
---

Vim

<!--more-->

## 安装 NeoVim

使用 Chocolatey 安装
```
cinst -y neovim
```

## NeoVim 配置文件

linux 位置：`$HOME\.config\nvim\init.vim`

Windows 位置：`$LOCALAPPDATA\nvim\init.vim`

```
:echo $LOCALAPPDATA\nvim\init.vim

C:\Users\ssl\AppData\Local\nvim\init.vim
```

## 安装插件管理器 vim-plug

vim-plug 是一个单独的文件 plug.vim，

从这里 https://github.com/junegunn/vim-plug 下载，

然后保存到 neovim 的 autoload 目录里即可

插件的安装目录：$LOCALAPPDATA\nvim\plugged

## 给 NeoVim 安装 Markdown 预览插件

打开 init.vim 配置文件，增加：
```
call plug#begin()
Plug 'rhysd/nyaovim-markdown-preview'
call plug#end()

"自动开始 Markdown 预览
let g:markdown_preview_auto = 1

"预览 Markdown 敏感度
let g:markdown_preview_eager = 1
```

在 NeoVim 里执行
```
//查看插件列表
:PlugStatus

//安装插件
:PlugInstall
```

安装成功后也可执行下列命令，需要先打开 .md 结尾的文件
```
:StartMarkdownPreview
:StotMarkdownPreview
```

## 安装 NyaoVim

NyaoVim 基于 NeoVim 做了进一步开发，需要先安装上面的 NeoVim

使用 npm 安装
```
npm install -g nyaovim
```

配置文件位置：$APPDATA\NyaoVim\nyaovimrc.html
```
:ehoc $APPDATA\NyaoVim\nyaovimrc.html

C:\Users\ssl\AppData\Roaming\NyaoVim\nyaovimrc.html
```

打开配置文件，写入 Markdown 预览插件
```
<dom-module id="nyaovim-app">
  <template>
    <style>
      /* CSS configurations here */
      .horizontal {
        display: flex;
        width: 100%;
        height: 100%;
      }
      neovim-editor {
        width: 50%;
        height: 100%;
      }
      markdown-preview {
        width: 50%;
        height: 100%;
      }
    </style>

    <!-- Component tags here -->
    <div class="horizontal">
      <neovim-editor id="nyaovim-editor" argv="[[argv]]"></neovim-editor>
      <!-- Put component -->
      <markdown-preview editor="[[editor]]"></markdown-preview>
    </div>

  </template>
</dom-module>
```
