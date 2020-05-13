---
title: Vim-自动完成插件 coc.nvim-gopls,vim-go
categories:
- Vim
tags:
- Vim
- coc.nvim
- gopls
- vim-go
date: 2020-03-14 13:10:21
---

Vim-自动完成插件 coc.nvim

<!--more-->

## 配置文件

包含用户配置文件和工作区配置文件，配置文件名都叫 `coc-settings.json`。

工作区配置文件的设置会覆盖用户配置文件的设置。

用户配置文件位置：

    nvim:
        $XDG_CONFIG_HOME/nvim
        $HOME/.config/nvim
    Vim:
        $HOME/.vim for vim

工作区配置文件位置：

    工作区目录下的 .vim 目录

安装json语言扩展coc-json，这样可以为配置文件 `coc-settings.json` 提供配置的补全
、验证等功能：
```vim
:CocInstall coc-json
```

使用命令打开编辑用户配置文件：
```vim
:CocConfig`
```

使用命令打开编辑工作区配置文件：
```vim
:CocLocalConfig
```

## 安装依赖插件

安装代码片段插件：
```vim
:CocInstall coc-snippets
```

安装列表插件：
```vim
:CocInstall CocList
```

```vim
:CocInfo
```


### gopls

gopls (pronounced: "go please") is the official language server(LSP) for the Go language.

The Language Server Protocol(LSP) allows any text editor to be extended with IDE-like features;
see https://langserver.org/ for details.

https://github.com/golang/tools/blob/master/gopls/README.md

#### 安装

https://github.com/golang/tools/blob/master/gopls/doc/user.md

通常由编辑器自动安装

命令行先切换到一个临时目录(不在 GOPATH 和任何模块里就可以)，然后使用下列命令安装
最新的稳定版本：
```bash
$ go get golang.org/x/tools/gopls@latest
```
* 不要使用 -u 参数，否则依赖会被升级到不兼容版本

如果发生错误：
```bash
$ go get golang.org/x/tools/gopls@latest
go: cannot use path@version syntax in GOPATH mode
```
则执行：
```bash
GO111MODULE=on go get golang.org/x/tools/gopls@latest
```

如果使用的是模块，则每个工作区目录下都应有个 `go.md` 文件

#### 配置

https://github.com/golang/tools/blob/master/gopls/doc/vim.md

Use vim-go ver 1.20+, with the following configuration:
```vim
let g:go_def_mode='gopls'
let g:go_info_mode='gopls'
```

Use coc.nvim, with the following coc-settings.json configuration:
```json
  "languageserver": {
    "golang": {
      "command": "gopls",
      "rootPatterns": ["go.mod", ".vim/", ".git/", ".hg/"],
      "filetypes": ["go"],
      "initializationOptions": {
        "usePlaceholders": true
      }
    }
  }
```

Other settings can be added in `initializationOptions` too.

The `editor.action.organizeImport` code action will auto-format code and add missing imports.
To run this automatically on save, add the following line to your init.vim:
```vim
autocmd BufWritePre *.go :call CocAction('runCommand', 'editor.action.organizeImport')
```

### vim-go

Vim 的 Go 语言开发插件

#### 安装

```
$ go get -u golang.org/x/tools/...
```

使用 vim-plug 安装 vim-go，配置文件增加:
```vim
Plug 'fatih/vim-go', { 'do': ':GoUpdateBinaries' }
```
然后 Vim 中执行 `:PlugInstall`

再安装 vim-go 所依赖的一些命令文件，Vim 中执行：
```vim
:GoInstallBinaries
```
是把这些可执行命令安装到了目录 `$GOPATH/bin` 下

#### 使用

https://github.com/fatih/vim-go/wiki

运行：
```vim
:GoRun %     " 运行当前文件
:GoRun       " 运行整个包
```

编译：
```
:GoBuild    " 编译当前文件，跳到第一个错误处
:GoBuild!   " 编译当前文件，但不跳到第一个错误处
```

相对于 GoRun，GoBuild 的不同之处：
* 不创建二进制文件，多次调用也不会污染你的工作区
* 自动进入源文件目录
* 编译错误自动显示到 QuickFix 窗口中
* 自动检测 GOPATH 路径，需要时则修改(检测 gb, Godeps 等项目..)
* 异步运行(Vim 8.0.xxx or NeoVim)

vim 中设置了 autowrite，则调用 GoBuild 时会自动保存文件：
```vim
set autowrite
```

定义 vim-go 快捷键：
```
autocmd FileType go nmap <leader>b  <Plug>(go-build)
autocmd FileType go nmap <leader>r  <Plug>(go-run)
```

定义 quickfix 快捷键：
```
map <C-n> :cnext<CR>
map <C-m> :cprevious<CR>
nnoremap <leader>a :cclose<CR>
```

指定 Vim-go 的错误列表只使用 quickfix 列表，不使用 location list：
```vim
let g:go_list_type = "quickfix"
```

### coc-snippets

https://github.com/neoclide/coc-snippets

`~\AppData\Local\coc\ultisnips\markdown.snippets`

`:CocList snippets` 查看 snippets 列表

`:CocCommand snippets.editSnippets` 编辑当前文件类型的 snippets 用户文件

`:CocCommand snippets.openSnippetFiles` 打开当前文件类型的 snippets 文件


### CocList

#### rg

CocList 搜索文件需安装 rg 或 ag，

rg 即 [ripgrep](https://github.com/BurntSushi/ripgrep)

ripgrep ：recursively searches directories for a regex pattern

windows 版的为一可执行文件，下载后放环境变量 PATH 目录下即可

Windows 版还分为编译时使用 MinGW (GNU) 还是 Microsoft Visual C++ (MSVC)，

Windows 下推荐使用后者， 需要已安装 [Microsoft VC++ 2015 redistributable](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

redhat 系 linux 可执行：
```
$ sudo dnf install ripgrep
```

## 问题

coc.nvim 提示不能下载：we encountered an issue downloading the language server

若是使用了代理，则需要 `coc-settings.json` 增加设置：

    "http.proxy": "http://127.0.0.1:1088"

注意 http 代理前面有 `http://`
