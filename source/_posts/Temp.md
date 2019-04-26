---
title: Temp - To Be Reorganize
categories:
  - Temp
tags:
  - Temp
---

Temp - To Be Reorganize

<!--more-->

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

## ssh

The authenticity of host 'github.com (13.229.188.59)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes

## Win10 磁盘占用过高

计算机\HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Enum\PCI\VEN_8086&DEV_A103&SUBSYS_505017AA&REV_31\3&b1bfb68&0&B8\Device Parameters\Interrupt Management\MessageSignaledInterruptProperties\

MSISupported 默认值 1 改为 0

## 网卡地址为 169.254.211.*

169.254.211.0 到 169.254.211.255 属于 Private Network，仅用于局域网，造成获取
到此地址的原因：
* dhcp 获取地址失败
* 手动指定的地址冲突

  连接特定的 DNS 后缀 . . . . . . . :
  本地链接 IPv6 地址. . . . . . . . : fe80::bcb1:c08c:d484:d33b%4
  自动配置 IPv4 地址  . . . . . . . : 169.254.211.59
  子网掩码  . . . . . . . . . . . . : 255.255.0.0
  默认网关. . . . . . . . . . . . . : 192.168.3.1

https://en.wikipedia.org/wiki/Private_network

## vim powerline font

```vim
" air-line
let g:airline_powerline_fonts = 1

if !exists('g:airline_symbols')
    let g:airline_symbols = {}
endif

" unicode symbols
let g:airline_left_sep = '?'
let g:airline_left_sep = '?'
let g:airline_right_sep = '?'
let g:airline_right_sep = '?'
let g:airline_symbols.linenr = '?'
let g:airline_symbols.linenr = '?'
let g:airline_symbols.linenr = '?'
let g:airline_symbols.branch = '?'
let g:airline_symbols.paste = 'ρ'
let g:airline_symbols.paste = 'T'
let g:airline_symbols.paste = '∥'
let g:airline_symbols.whitespace = 'Ξ'

" airline symbols
let g:airline_left_sep = ''
let g:airline_left_alt_sep = ''
let g:airline_right_sep = ''
let g:airline_right_alt_sep = ''
let g:airline_symbols.branch = ''
let g:airline_symbols.readonly = ''
let g:airline_symbols.linenr = ''
```

## Alt + Tab 背景透明度

HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\MultitaskingView\AltTabViewHost

新建两个 DWORD 值

    整个屏幕的透明度：BackgroundDimmingLayer_percent

    背景框的透明度：Grid_backgroundPercent，

其取值范围都是 0 到 100，实时生效无需重启

[Text](http://www.url.com)


## Alt + ESC

What Alt+Esc actually does is drop the active window to the bottom of the pile,
while effectively brings the next open window into view, the next windows which
is not minimized;

sometimes, however, this means you'll need to click Alt+Esc again to bring the
window into focus so you can start typing in it or otherwise actively working in
the window.

## SSH

```bash
➜  SSRSpeed (master) ssh-keygen -f /d/Documents/TopSecret/GCP_Private_Key_SSH_Format.ppk -p
Enter old passphrase:
Enter new passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved with the new passphrase.

➜  SSRSpeed (master) ssh -i /d/Documents/TopSecret/GCP_Private_Key_SSH_Format.ppk shenlin1970@34.92.146.185
```

## 更改用户 SHELL

```bash
$ chsh -l
$ cat /etc/shells
```

User account modifications will not be saved if you have opened /etc/passwd (vim
/etc/passwd) when you try to change the info.
```bash
$ sudo chsh -s /bin/zsh
```

Alternative: try with usermod (as your username):
```bash
$ sudo usermod -s /bin/zsh shenlin1970
```

If this doesn't work either, edit /etc/passwd by hand.
please use `vipw` instead of vim as they set the appropriate file locks.
```bash
$ sudo vipw
```

Then:
1.exit for server
2.restart terminal
3.log into server, and check by echo $SHELL – in successful case it changed :)

## change shell withoud sudo or su

prevents locked-down accounts from changing their shells, and selectively lets
people use chsh themselves WITHOUT sudo or su

https://serverfault.com/questions/202468/changing-the-shell-using-chsh-via-the-command-line-in-a-script


## SSR

```bash
➜  ~ systemctl status shadowsocks-r.service
● shadowsocks-r.service - LSB: Fast tunnel proxy that helps you bypass firewalls
   Loaded: loaded (/etc/rc.d/init.d/shadowsocks-r; bad; vendor preset: disabled)
   Active: active (exited) since Thu 2019-04-25 09:50:05 CST; 4h 11min ago
     Docs: man:systemd-sysv-generator(8)
  Process: 2966 ExecStart=/etc/rc.d/init.d/shadowsocks-r start (code=exited, status=0/SUCCESS)

Apr 25 09:50:03 instance-1 systemd[1]: Starting LSB: Fast tunnel proxy that helps you bypass firewalls...
Apr 25 09:50:04 instance-1 shadowsocks-r[2966]: 2019-04-25 09:50:04 INFO     util.py:94 loading libcrypto from libcrypto.so.10
Apr 25 09:50:05 instance-1 shadowsocks-r[2966]: 2019-04-25 09:50:05 INFO     shell.py:74 ShadowsocksR SSRR 3.2.2 2018-05-22
Apr 25 09:50:05 instance-1 shadowsocks-r[2966]: IPv6 support
Apr 25 09:50:05 instance-1 shadowsocks-r[2966]: Starting ShadowsocksR success
Apr 25 09:50:05 instance-1 systemd[1]: Started LSB: Fast tunnel proxy that helps you bypass firewalls.

➜  ~ sudo python /usr/local/shadowsocks/server.py -c /etc/shadowsocks-r/config.json -d stop
IPv6 support
2019-04-25 14:01:59 INFO     shell.py:74 ShadowsocksR SSRR 3.2.2 2018-05-22
stopped

➜  ~ systemctl status shadowsocks-r.service
● shadowsocks-r.service - LSB: Fast tunnel proxy that helps you bypass firewalls
   Loaded: loaded (/etc/rc.d/init.d/shadowsocks-r; bad; vendor preset: disabled)
   Active: active (exited) since Thu 2019-04-25 09:50:05 CST; 4h 12min ago
     Docs: man:systemd-sysv-generator(8)
  Process: 2966 ExecStart=/etc/rc.d/init.d/shadowsocks-r start (code=exited, status=0/SUCCESS)

Apr 25 09:50:03 instance-1 systemd[1]: Starting LSB: Fast tunnel proxy that helps you bypass firewalls...
Apr 25 09:50:04 instance-1 shadowsocks-r[2966]: 2019-04-25 09:50:04 INFO     util.py:94 loading libcrypto from libcrypto.so.10
Apr 25 09:50:05 instance-1 shadowsocks-r[2966]: 2019-04-25 09:50:05 INFO     shell.py:74 ShadowsocksR SSRR 3.2.2 2018-05-22
Apr 25 09:50:05 instance-1 shadowsocks-r[2966]: IPv6 support
Apr 25 09:50:05 instance-1 shadowsocks-r[2966]: Starting ShadowsocksR success
Apr 25 09:50:05 instance-1 systemd[1]: Started LSB: Fast tunnel proxy that helps you bypass firewalls.
```

## v2ray

```log
2019/04/25 14:24:55 [Warning] v2ray.com/core/transport/internet/websocket:
failed to serve http for WebSocket > accept tcp [::]:53501: use of closed network connection

2019/04/25 14:24:56 [Warning] v2ray.com/core: V2Ray 4.18.0 started
```

## tmux

https://my.oschina.net/u/144160/blog/875643

tmux里面用鼠标滚轮来卷动窗口内容

在 tmux里面，因为每个窗口(tmux window)的历史内容已经被tmux接管了，所以原来
console/terminal提供的Shift+PgUp/PgDn所显示的内容并不是当前窗口的历史内容，所以
要用C-b [ 进入copy-mode，然后才能用PgUp/PgDn/光标/Ctrl-S等键在copy-mode中移动。

如果要启用鼠标滚轮来卷动窗口内容的话，可以按C-b :然后输入
    setw mode-mouse on
这就可以了。如果要对所有窗口开启的话:
    setw -g mode-mouse on
(这种情况下，Vi/Emacs等全屏程序并不受影响，还可以自己接管滚轮事件)

也可以加到~/.tmux.conf里面
     set-window-option -g mode-mouse on
(setw其实是set-window-option的别名)
