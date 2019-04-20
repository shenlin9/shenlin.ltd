
## git ssh

```
The authenticity of host 'github.com (13.229.188.59)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,13.229.188.59' (RSA) to the list of known hosts.
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 7.52 KiB | 3.76 MiB/s, done.
Total 4 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To github.com:shenlin9/config_bak.git
   17796be..de48bb0  master -> master
```

## git

让 Git 支持 utf-8 编码
```
$ git config --global core.quotepath false  		# 显示 status 编码
$ git config --global gui.encoding utf-8			# 图形界面编码
$ git config --global i18n.commit.encoding utf-8	# 提交信息编码
$ git config --global i18n.logoutputencoding utf-8	# 输出 log 编码
$ export LESSCHARSET=utf-8
```
# 最后一条命令是因为 git log 默认使用 less 分页，所以需要 bash 对 less 命令进行 utf-8 编码

让 ls 命令可以显示中文名称

修改 git-completion.bash 文件：

# 在文件末尾处添加一行
alias ls="ls --show-control-chars --color"


## wsltty

wsltty - Mintty Terminal for WSL(Windows Subsystem for Linux)

wsltty 启动方式：
%LOCALAPPDATA%\wsltty\bin\mintty.exe -i "%LOCALAPPDATA%\wsltty\wsl.ico" --WSL="Ubuntu-18.04" --configdir="%APPDATA%\wsltty" -~   %*


wsltty 可以设置背景图，我们编辑：%APPDATA%/wsltty/config 文件，在最后加一行：
Background=_/mnt/c/Users/XXXXX/sierra.bmp,39
可以给我们的 WSL 设置一张背景图片，Background 设置中，开头的下划线代表图片的显示方式是自动缩放，铺满窗口，后面是一个图片的路径名，路径名为 /mnt/c/ 开头的 wsl 的路径格式，逗号后面的 39 代表 darken，将图片调暗，不然太亮没法看。

## Ubuntu

Ubuntu1804 /?
1804 是版本号

更改默认 wsl 登录用户
Ubuntu1804 config --default-user shenlin

WSL 子系统是基于 LxssManager 服务运行的。只需要将 LxssManager 重启即可。
管理员身份运行
net stop LxssManager
net start LxssManager

Windows 官方方法：
wsl -u <Username>
wsl --user <Username>

## wsl.conf

WSL 导入 Windows 的环境变量 %PATH% 时因为空格出错：
-sh: 6: export: Files/Python37/Scripts:/mnt/c/Program: bad variable name

一种方法是禁止导入

Windows Build 17093 之后开始支持 /etc/wsl.conf 文件

Windows Build 17713 之后才支持的选项：

    enabled
    Setting this key will determine whether WSL will support launching Windows
    processes.
    default ：true

    appendWindowsPath
    Setting this key will determine whether WSL will add Windows path elements to the $PATH environment variable.
    default ：true

https://docs.microsoft.com/en-us/windows/wsl/wsl-config

如果 build 低于 17713 可以通过注册表禁止：
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Lxss]
"AppendNtPath"=dword:00000000

讨论：https://github.com/Microsoft/WSL/issues/1640

## windows 

C:\Users\T460P>echo %USERPROFILE%
C:\Users\T460P

C:\Users\T460P>echo %LOCALAPPDATA%
C:\Users\T460P\AppData\Local

C:\Users\T460P>echo %APPDATA%
C:\Users\T460P\AppData\Roaming



## vim 快速启动

--noplugin	Skip loading plugins.  Resets the 'loadplugins' option.
		{not in Vi}
		Note that the |-u| argument may also disable loading plugins:
			argument   load: vimrc files  plugins  defaults.vim ~
			(nothing)	     yes	        yes	        yes
			-u NONE		     no		        no	        no
			-u DEFAULTS	     no		        no	        yes
			-u NORC		     no		        yes	        no
			--noplugin	     yes	        no	        yes


"C:\Program Files (x86)\Vim\vim81\gvim.exe" --noplugin D:\git-repo\shenlin.ltd\source\_posts\Temp.md


## vim 中英文自动切换

好像没什么用，因为会自动切换

```vim
"插入模式进入普通模式自动切换为英文输入法
function! Fcitx2en()
   let s:input_status = system("fcitx-remote")
   if s:input_status == 2
      let g:input_toggle = 1
      let l:a = system("fcitx-remote -c")
   endif
endfunction

"普通模式进入插入模式自动切换为中文输入法
function! Fcitx2zh()
   let s:input_status = system("fcitx-remote")
   if s:input_status != 2 && g:input_toggle == 1
      let l:a = system("fcitx-remote -o")
      let g:input_toggle = 0
   endif
endfunction

"let g:input_toggle = 1
"set timeoutlen=150
"autocmd InsertLeave * call Fcitx2en()
"autocmd InsertEnter * call Fcitx2zh()
```

## wsl 使用 win10 的 ssr 代理

/etc/profile 在文件最后添加（具体端口配置和ss 客户端保持一致）

export http_proxy=http://127.0.0.1:1080
export https_proxy=http://127.0.0.1:1080
export ftp_proxy=http://127.0.0.1:1080

source /etc/profile 让配置生效


## 删除的路径

D:\Install-Packages\cmder\vendor\git-for-windows\bin
C:\cygwin64\bin

sys：
D:\Install-Packages\cmder\vendor\git-for-windows\usr\bin

## tmux 启用鼠标操作

setw -g mouse
set-option -g history-limit 20000
set-option -g mouse on
bind -n WheelUpPane select-pane -t= \; copy-mode -e \; send-keys -M
bind -n WheelDownPane select-pane -t= \; send-keys -M

https://www.youtube.com/watch?v=xTplsyQaGFs


## 附 Git Bash 终端快捷键

按键	    效果
Alt + Enter	全屏
关闭窗口	Ctrl + D（需当前行没有字符才可用）
清屏	    Ctrl + L
打开终端	右键快捷方式，在属性中自行设置
复制粘贴	Ctrl + Shift + C/V（v2.20.0 版本后才支持）

其他快捷键遵循 Bash 标准快捷键，例如 
Ctrl + R 搜索历史命令
exit 退出终端
clear 和 reset 清屏
Ctrl + e/a 光标到行首尾
等等可自行搜索。

## netsh winhttp

```
C:\Users\T460P>netsh winhttp

下列指令有效:

此上下文中的命令:
?              - 显示命令列表。
dump           - 显示一个配置脚本。
help           - 显示命令列表。
import         - 导入 WinHTTP 代理服务器设置。
reset          - 重置 WinHTTP 设置。
set            - 配置 WinHTTP 设置。
show           - 显示当前设置。

若需要命令的更多帮助信息，请键入命令，接着是空格，
后面跟 ?。

C:\Users\T460P>netsh winhttp show proxy

当前的 WinHTTP 代理服务器设置:

    代理服务器:  127.0.0.1:1088
    绕过列表     :  niuza.com;guancha.cn;jandan.net;toutiao.com;tmall.com;taobao.com;jd.com;zhihu.com;alicdn.com;youku.com;iqiyi.com;bilibili.com;qq.com;baidu.com;google.com;<local>


C:\Users\T460P>netsh winhttp reset proxy

当前的 WinHTTP 代理服务器设置:

    直接访问(没有代理服务器)。


C:\Users\T460P>netsh winhttp import proxy source=ie

当前的 WinHTTP 代理服务器设置:

    代理服务器:  127.0.0.1:1088
    绕过列表     :  niuza.com;guancha.cn;jandan.net;toutiao.com;tmall.com;taobao.com;jd.com;zhihu.com;alicdn.com;youku.com;iqiyi.com;bilibili.com;qq.com;baidu.com;google.com;<local>
```

## Windows 下安装 GCC

使用 cygwin
使用 mingw64
使用 msys2
使用 tdm-gcc

## cygwin, mingw, msys2



## vim

```bash
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim
```

```vim
set nocompatible              " be iMproved, required
filetype off                  " required

" set the runtime path to include Vundle and initialize
set rtp+=~/.vim/bundle/Vundle.vim
call vundle#begin()
" alternatively, pass a path where Vundle should install plugins
"call vundle#begin('~/some/path/here')

" let Vundle manage Vundle, required
Plugin 'VundleVim/Vundle.vim'


" All of your Plugins must be added before the following line
call vundle#end()            " required
filetype plugin indent on    " required
```
