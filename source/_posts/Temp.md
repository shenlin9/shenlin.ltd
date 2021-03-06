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

## Systemctl & Loaded

According to RHEL7 System Administration Guide
(https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/System_Administrators_Guide/sect-Managing_Services_with_systemd-Services.html#sect-Managing_Services_with_systemd-Services-List)

The following command should list all loaded units

```bash
$ systemctl list-units --type service --all
```

But in fact it doesn't list all loaded services, only those which are enabled OR
active OR (active AND enabled).

For example:
```bash
$ systemctl list-units --type service --all | grep httpd

$ systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:httpd(8)
           man:apachectl(8)
```
Is it the way it is supposed to be or it might be documentation/code bug?

问题描述：
按文档所说 `systemctl list-units --type service --all` 应该显示全部被 loaded 的
单元，但实际情况是只要被 disabled 的单元即使 Loaded 了也没显示，证据就是这个：
```bash
$ systemctl list-units --type service --all | grep httpd
# 没有输出结果，即按文档所说 httpd 应该未被 Loaded
# 但使用下面的 status 命令又显示 httpd 是被 Loaded 的，所以这两个命令有一个是错
# 的
$ systemctl status httpd
● httpd.service - The Apache HTTP Server
   Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
     Docs: man:httpd(8)
           man:apachectl(8)
```

**Loaded**：

"Loaded" means that systemd has the read the unit from disk into memory. This
will happen whenever you "look" at the unit, e.g. with status, when the unit is
started, or when the unit is a dependency of another unit that is loaded.

The misunderstanding here is that 'systemctl status' will always show the unit
as "loaded", because systemd loads the unit to display the status. If the unit
is not needed for anything else, it will be unloaded immediately after.

If you want to display all a list of all possible units found on disk, use
'systemctl list-unit-files'.

**enable/disable**

```bash
[root@instance-1]/home/shenlin1970# systemctl enable v2ray
Created symlink from /etc/systemd/system/multi-user.target.wants/v2ray.service to /etc/systemd/system/v2ray.service.

[root@instance-1]/home/shenlin1970# systemctl status v2ray
● v2ray.service - V2Ray Service
   Loaded: loaded (/etc/systemd/system/v2ray.service; enabled; vendor preset: disabled)
   Active: inactive (dead)

[root@instance-1]/home/shenlin1970# systemctl disable v2ray
Removed symlink /etc/systemd/system/multi-user.target.wants/v2ray.service.

[root@instance-1]/home/shenlin1970# systemctl status v2ray
● v2ray.service - V2Ray Service
   Loaded: loaded (/etc/systemd/system/v2ray.service; disabled; vendor preset: disabled)
   Active: inactive (dead)
```

## yum

```yum
yum -e epel-release
yum remove knerl-1.3.x
```

## certbot

```
[root@instance-1]/home/shenlin1970# certbot certonly --standalone -d shenlin.ga
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Plugins selected: Authenticator standalone, Installer None
Enter email address (used for urgent renewal and security notices) (Enter 'c' to
cancel): shenlin1970@gmail.com
Starting new HTTPS connection (1): acme-v02.api.letsencrypt.org

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server at
https://acme-v02.api.letsencrypt.org/directory
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(A)gree/(C)ancel: A

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you be willing to share your email address with the Electronic Frontier
Foundation, a founding partner of the Let's Encrypt project and the non-profit
organization that develops Certbot? We'd like to send you email about our work
encrypting the web, EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: N

Obtaining a new certificate
Performing the following challenges:
http-01 challenge for shenlin.ga
Waiting for verification...
Cleaning up challenges
Resetting dropped connection: acme-v02.api.letsencrypt.org

IMPORTANT NOTES:
 - Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/shenlin.ga/fullchain.pem
   Your key file has been saved at:
   /etc/letsencrypt/live/shenlin.ga/privkey.pem
   Your cert will expire on 2019-07-25. To obtain a new or tweaked
   version of this certificate in the future, simply run certbot
   again. To non-interactively renew *all* of your certificates, run
   "certbot renew"
 - Your account credentials have been saved in your Certbot
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Certbot so
   making regular backups of this folder is ideal.
 - If you like Certbot, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le

```

##

linux系统设置http/https proxy的方法，在/etc/bashrc或者/etc/profile中添加如下环境变量：
```
export http_proxy=http://1.1.1.1:8082
export https_proxy=http://1.1.1.1:8082
```

## linux network config

在 Linux 改 IP /default gateway/ 和 DNS 等設定

1. 修改 IP address
(先用
```
# ifconfig -a⋯⋯
```
看系統可用的網路 device, 假如是 eth0 的話)

及時生效:
```
# ifconfig eht0 192.168.0.20 netmask 255.255.255.0
```
啟動時生效:
```
# vi /etc/sysconfig/netowrk-scripts/ifcfg-eth0
DEVICE=eth0
BOOTPROTO=none
BROADCAST=192.168.0.255
IPADDR=192.168.0.30 <== 改成新 IP
NETMASK=255.255.255.0 <== 改為新的 netmask
GATEWAY=192.168.0.254 <== 改為新的 gateway
```

2. 修改 default gateway

及時生效
```
# route add default gw 192.168.0.1 
```
啟動生效:
```
# vi /etc/sysconfig/network-scripts/ifcfg-eth0
GATEWAY=192.168.0.254
```

3. 修改 DNS

```
# vi /etc/resolv.conf (修改後可及時生效, 重新啟動也有效)
search
nameserver 168.95.1.1
```

4. 重新啟動網路服務
# /etc/init.d/network restart

## CL电容型号解读

CL21 185J 250V

聚酯（涤纶）电容
符号：CL
电容量：40p--4u
额定电压：63--630V
主要特点：小体积，大容量，耐热耐湿，稳定性差
应用：对稳定性和损耗要求不高的低频电路

J 和 K 分别是容量的允许偏差 分别为±5％ 和±10％

185 前面的 18 是有效数字，后面的 5 代表 10 的几次幂，即 18 乘以 10 的 5 次方
，为 1,800,000 pf，等于 1.8uf

CL21 225J 250V  容量 22，即 2.2μF 

## 电容单位

在国际单位制里，电容的单位是法拉，简称法，符号是F，

由于法拉这个单位太大，所以常用的电容单位有毫法（mF）、微法（μF）、纳法（nF）和
皮法（pF）等，换算关系是：
1法拉（F）= 1000毫法（mF）=1000000微法（μF）
1微法（μF）= 1000纳法（nF）= 1000000皮法（pF）。

## 电容类型

从原理上分为：无极性可变电容、无极性固定电容、有极性电容等

无极性可变电容
制作工艺：可旋转动片为陶瓷片表面镀金属薄膜，定片为镀有金属膜的陶瓷底；动片为同轴金属片，定片为有机薄膜片作介质
优点：容易生产，技术含量低。
缺点：体积大，容量小
用途：改变震荡及谐振频率电路。调频、调幅、发射/接收电路 [2] 

无极性无感CBB电容
制作工艺：2层聚丙乙烯塑料和2层金属箔交替夹杂然后捆绑而成。
优点：无感，高频特性好，体积较小
缺点：不适合做大容量，价格比较高，耐热性能较差。
用途：耦合/震荡，音响，模拟/数字电路，高频电源滤波/退耦 [2] 

无极性CBB电容
制作工艺：2层聚乙烯塑料和2层金属箔交替夹杂然后捆绑而成。
优点：有感，高频特性好，体积较小
缺点：不适合做大容量，价格比较高，耐热性能较差。
用途：耦合/震荡，模拟/数字电路，电源滤波/退耦

无极性瓷片电容
制作工艺：薄瓷片两面渡金属膜银而成。
优点：体积小，耐压高，价格低，频率高（有一种是高频电容）
缺点：易碎，容量低
用途：高频震荡、谐振、退耦、音响 [2] 

无极性云母电容
制作工艺：云母片上镀两层金属薄膜
优点：容易生产，技术含量低。
缺点：体积大，容量小用途：震荡、谐振、退耦及要求不高的电路无极性独石电容体积比CBB更小，其他同CBB，有感
用途：模拟/数字电路信号旁路/滤波，音响 [2] 

有极性电解电容
制作工艺：两片铝带和两层绝缘膜相互层叠，转捆后浸在电解液中。
优点：容量大。
缺点：高频特性不好。
用途：低频级间耦合、旁路、退耦、电源滤波、音响 [2] 


从材料上可以分为：CBB电容（聚乙烯），涤纶电容、瓷片电容、云母电容、独石电容、电解电容、钽电容等 [2]  。

钽电容
制作工艺：用金属钽作为正极，在电解质外喷上金属作为负极。
优点：稳定性好，容量大，高频特性好。
缺点：造价高。
用途：高精度电源滤波、信号级间耦合、高频电路、音响电路 [2] 

聚酯（涤纶）电容
符号：CL
电容量：40p--4u
额定电压：63--630V
主要特点：小体积，大容量，耐热耐湿，稳定性差
应用：对稳定性和损耗要求不高的低频电路 [2] 

聚苯乙烯电容
符号：CB
电容量：10p--1u
额定电压：100V--30KV
主要特点：稳定，低损耗，体积较大
应用：对稳定性和损耗要求较高的电路 [2] 

聚丙烯电容
符号：CBB
电容量：1000p--10u
额定电压：63--2000V
主要特点：性能与聚苯相似但体积小，稳定性略差
应用：代替大部分聚苯或云母电容，用于要求较高的电路 [2] 

云母电容
符号：CY
电容量：10p--0。1u
额定电压：100V--7kV
主要特点：高稳定性，高可靠性，温度系数小
应用：高频振荡，脉冲等要求较高的电路 [2] 

高频瓷介电容
符号：CC
电容量：1--6800p
额定电压：63--500V
主要特点：高频损耗小，稳定性好
应用：高频电路 [2] 

低频瓷介电容
符号：CT
电容量：10p--4。7u
额定电压：50V--100V
主要特点：体积小，价廉，损耗大，稳定性差
应用：要求不高的低频电路 [2] 

玻璃釉电容
符号：CI
电容量：10p--0。1u
额定电压：63--400V
主要特点：稳定性较好，损耗小，耐高温（200度）
应用：脉冲、耦合、旁路等电路 [2] 


钽电解电容（CA）、铌电解电容（CN）
电容量：0。1--1000u
额定电压：6。3--125V
主要特点：损耗、漏电小于铝电解电容
应用：在要求高的电路中代替铝电解电容 [2] 
空气介质可变电容器
可变电容量：100--1500p
主要特点：损耗小，效率高；可根据要求制成直线式、直线波长式、直线频率式及对数式 等
应用：电子仪器，广播电视设备等 [2] 

薄膜介质可变电容器
可变电容量：15--550p
主要特点：体积小，重量轻；损耗比空气介质的大
应用：通讯，广播接收机等 [2] 
薄膜介质微调电容器
符号： 可变电容量：1--29p
主要特点：损耗较大，体积小
应用：收录机，电子仪器等电路作电路补偿 [2] 

陶瓷介质微调电容器
符号： 可变电容量：0。3--22p
主要特点：损耗较小，体积较小
应用：精密调谐的高频振荡回路 [2]

## 铝电解电容

铝电解电容
符号：CD
电容量：0。47--10000u
额定电压：6。3--450V
主要特点：体积小，容量大，损耗大，漏电大
应用：电源滤波，低频耦合，去耦，旁路等 [2] 

耐压的选择:由于铝电解电容的误差较大,在耐压选取方面设计时会留有很大的余量例如:12V
电源部分常用16V铝电解电容,5V电源常用 10V ，3.3V选用6.3V,3.3V以下选用6.3V或者4V(
这种很少见)这是厂商选择的一般规律,我们在板卡上也会见到用在12V电源上的 25V铝电解
电容,甚至在CPU 1.45V的滤波部分看到10V的电解电容.所以原铝电解电容耐压只做为参考,
选用电容耐压的唯一的标准是电路的电压, 如果选用固体电容,只要电路电压低于固体电容
耐压即可,不需要考虑余量(事实上电容设计者已经根据常用电源电压留好了余量)

电解电容的耐高温较差，因此焊接时也要注意，避免时间过长温度升高导致爆炸。

## 固体电容代换电解电容的原则

From: http://hudsontang.blogspot.com/2010/11/blog-post_15.html

目前大多数影音及计算机产品中配置以下几组电压12V、5V、3.3V、2.5V、 1.8V、及1.8V以
下.首先我们强调一下5V电源在板卡 的数字电路系统中主要负责各类输入输出接口的供电，
其分布范围是比较少，电容的损坏率也相当低．所以,在正常情况下电脑板卡和以数字逻辑
电路为主的电路板 中,在小批量维修替换时10V的电解电容完全可以使用6.3V的电容替代.

耐压的选择:由于铝电解电容的误差较大,在耐压选取方面设计时会留有很大的余量例如:12V
电源部分常用16V铝电解电容,5V电源常用 10V ，3.3V选用6.3V,3.3V以下选用6.3V或者4V(
这种很少见)这是厂商选择的一般规律,我们在板卡上也会见到用在12V电源上的 25V铝电解
电容,甚至在CPU 1.45V的滤波部分看到10V的电解电容.所以原铝电解电容耐压只做为参考,
选用电容耐压的唯一的标准是电路的电压, 如果选用固体电容,只要电路电压低于固体电容
耐压即可,不需要考虑余量(事实上电容设计者已经根据常用电源电压留好了余量)

容量的选择,电容容量的选择是根据电路中的电流(即功耗)来确定的,如CPU是主板中的耗电
之王,在其周边我们就见到了密密麻麻的电解电容和高频 瓷片电容,在显卡的GPU附近亦是如
此.同样由于电解电容的误差大和老化后容量减小较大,在容量选择上也会留有很大的余量.
固体电容容量几乎不会减小,不 用考虑老化后容量减小的问题,再者ESR值明显优于铝电解电
容,所以在容量选择上固体电容有很大的空间,根据经验一般可选择为铝电解容量的四分之一
或者更 大,当然这个值不是绝对的,略有偏差,无关要紧.

大家对电解电容比较熟悉,对于电容的认识往往只记得容量及耐压值,没错，但忘却了关于电
容品质的决定性因素[电容的材质]，当替换选择电解电容 时，在体积允许的情况下,按照与
原使用型号的容量耐压贴近的，高压替换低压，高容量替换低容量，都是正确的认识,但在
固体电容的选择上，是不能按照这样传 统的替换概念的，由于材料及工艺不同,在同等耐压
及容量情况下,电解电容和固体电容对比,固体电容的体积要大出电解电容一倍以上.由于固
体电容材料价格较 铝电解电容的材料价格贵出很多,越大的越贵,固体已经很贵啦,没有必要
做得那么大.更重要的是由于固体电容优秀的性能决定了小容量即可胜任更恶劣工作环 境,

纯固态材料决定了其寿命更长,误差更小,其出厂时的参数在连续工作数万小时后,仍可维持
不变.但铝电解电容在工作两千小时后,电解液将慢慢出现干涸现象, 容量变小,随着时间推
移,电路系统将变得不稳定,如运行变慢死机等等固体电容强调的是低ESR，高温时性能不变.
所以更换固体电容，大家不要老觉得容量够 不够啦，电压会不会太低啊这些概念性的错误.

下面给家例举一些比拟常用的电解换固体的比例值：

1.CPU供电类电容，此位置普通原来均是6.3V-10的电解（用到10V的电解普通都是些耐压不
达标，容量不合格的劣质产品来替代6.3V的，这些就不用太在意耐压了，普通都是用6.3V的
电解，改换固体，我们就能够依据CPU的实践电压来改换，就这个道理，估量至今没哪个兄
弟的CPU电压曾经超频到了4V以上的，所以2.5V的固体电容曾经完整够了，更何况2.5V的固
体实践耐压到达了3.5普遍的，这就是高耐压的表现）

2200UF/6.3V-3300UF/6.3V 电解交换固体电容 1200UF/4V 1500UF/2.5V 固体1500UF/6.3V
电解交换固体电容 820UF/6.3V/4V/2.5V

2.CPU滤波类

1500UF/16V-2200UF/16V 电解交换固体电容 16V330UF （此处大家常常被原来电解的高容量
所迷惑，问的最多的是容量够不够，呵呵，其实大可放心，之前说过，固体的材质是半导体
，不是电解类的东西，容量比值大致参照下1：3－3.5之间就能够了）

3.最常用的1000UF/6.3，普遍散布与内存插槽，AGP插槽，PCI插槽，此类电解换固体：
560UF/4V 470UF/6V

4.另外一些常用的，470UF/16V 电解换固体 180UF/16v 220uf/10v电解换固体180UF/16V


以上就根本掩盖了比拟常用的主板电解类的换固体的计划，主要目的是通知大家，固体改换
电解一定要修正的概念，第1：要留意实践电容位置的电压，第2：不要过火的强调容量来交
换电解


当然在以上的交换准绳之中还要优先选择批次比拟好的型号，就是电容的分类啦，呵呵，给
大家个规律～

*******************

(1)用于滤波电路的电解电容，一般来讲只要耐压、耐温相同，稍大容量的电容可代稍小的
，但有些电路电容值差不可太悬殊。比如交流市电整流滤波电 容，原机为21英寸时，采用
100UF/400V电解电容滤波，不宜用470UF/400V代用，因为电容容量太大时会造成开机瞬间对
整流桥堆等件的冲击 电流过大，造成元件损坏。

(2)起定时作用的电容要尽量用原值代用。若容量小，就采用并联方法解决，容量大时，串
联解决。

(3)代用电容在耐压，温度系数方面不能低于原电容。

(4)应急维修时可用质量可靠的有极性电解电容等值替代钽电容。

(5)不能用有极性电解电容取代无极性电解电容。当无极性电解电容较小时，可用其他无极
性电容代换。

(6)行逆程电容若损坏，可在容量方面灵活掌握，只要保证代换后逆程电容的总容量与原总
容量等值就行。

## Wrire

solid-core wire 单股实心线

stranded wire 多股绞线

DIP : Dual In-line Package 双列直插
      Double In-line Package

## Hexo Front-matter

Front-matter 是文件最上方以 --- 分隔的区域，用于指定个别文件的变量

子分类：
```
- Linux
- Tools
```

并列分类：
```
categories:
- [Linux]
- [Tools]
```

并列+子分类：
```
categories:
- [Linux, Hexo]
- [Tools, PHP]
```

## Surround.vim

how to?
实现 `<leader>` 键映射 `ysiw`，然后按 `'`， `"` 则分别添加单引号和双引号

## 无线电英语

Roger: 收到

approach: coming to land

All clear：周围安全

Affirmative：是（表肯定）

Negative：否（表否定）

Over：完毕（等待回话）

Copy：明白，清楚

Say Again：再说一遍

Need back up：需要支援

Cover me：掩护我

Fire in the hole：小心手雷

Enemy down：敌人被消灭

## vimrc

```vim
let g:input_toggle = 1

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

"set timeoutlen=150
"autocmd InsertLeave * call Fcitx2en()
"autocmd InsertEnter * call Fcitx2zh()

```

## U 型批头

U型规格按一字宽度计算。

U3  1.5mm
U4  1.8mm
U5  2.0mm
U6  2.3mm

## Y 型批头

Y0  3 
Y1  4
Y2  5
Y3  6

## 去IOE

去IOE，即去掉以IBM为代表的小型机、以甲骨文为代表的数据库、以EMC为代表的存储设备
，代之以相应的国产或开源产品

I指的是IBM的小型机
O指的是Oracle的数据库
E指的是EMC的高端存储。

IOE 产品，多是用在金融、证券、电信、保险、电力等传统行业，互联网企业，使用多是定
制服务器，开源软件等。比如操作系统使用 Linux，存储使用开源分布式存储，数据库使用
MySQL 或是 PostGreSQL 等。

## Open Hardware

开源硬件

"Open hardware" 或 "open source hardware" 指的是物理对象的设计规范，以任何人都可
以研究、修改、创建和分发所述对象的方式授权。

"Open hardware" 的 “源代码”指的是：电路图、蓝图、逻辑设计、计算机辅助设计(CAD)
图纸或文件等。

https://opensource.com/resources/what-open-hardware

## OSHWA

Open Source Hardware Association 开源硬件协会

Sino：bit是一款用于中国计算机教育的单板微控制器。 这是第一个中国 OSHWA 认证项目
，基于 Calliope mini 并获得 Calliope mini项目的许可。

Sino：bit 由开源硬件传播者和 DIY 爱好者 Naomi Wu 创建，网名机械妖姬(Naomi 'SexyCyborg' Wu)。

## English

Learning leads to success and Sharing brings you recognition.

Wall Clock Time  经过时间

## SSH

@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:rhzSxr8z4iDDHI8xc9LPmHnRGkCjQK4tFRWy5+lq/ZU.
Please contact your system administrator.
Add correct host key in /c/Users/T460P/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /c/Users/T460P/.ssh/known_hosts:5
ECDSA host key for 34.92.146.185 has changed and you have requested strict checking.
Host key verification failed.

## 端口

检查端口被哪个进程占用
```
# netstat -lnp|grep 22
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      2849/sshd           
tcp6       0      0 :::22                   :::*                    LISTEN      2849/sshd           
unix  2      [ ACC ]     STREAM     LISTENING     22703    1426/NetworkManager  /var/run/NetworkManager/private-dhcp

# ps 2849
  PID TTY      STAT   TIME COMMAND
 2849 ?        Ss     0:00 /usr/sbin/sshd -D
```

## Modulation 调制

https://www.youtube.com/watch?v=Lll53ihvAaM

电磁波频率和能量的关系：E = hv， v 频率

人说话的声音频率很低，因此能量也很低，而电磁波若以这么低的频率传输，则传输距离会
很近，因此为了使电磁波传送的更远，就要提高其频率，使电磁波具有高能量，这就是调制。

一张纸扔不远，一个纸团可以扔的远

carrier signal 载波信号

信号的三个属性：
* amplitude 振幅
* frequency 频率
* phase 相

frequency modulation：carrier signal 根据 message signal 变化而变化

## SSH

```
➜  shenlin.ltd (master) gcp
The authenticity of host '34.92.146.185 (34.92.146.185)' can't be established.
ECDSA key fingerprint is SHA256:rhzSxr8z4iDDHI8xc9LPmHnRGkCjQK4tFRWy5+lq/ZU.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '34.92.146.185' (ECDSA) to the list of known hosts.
Last login: Sun Jun  2 16:07:12 2019 from 74.125.41.104
```

## 铅酸电池

概况：
* 48V 铅酸充电器电压应介于 55V 到 59.6V 之间，标准为 57.6 V。
* 一般的48伏铅酸充电器电压是59伏，电池充满后会慢慢下降到56伏左右。静止2小时后
  52.6伏左右，这个电池就是很好。电池用久了，2小时后52伏就不错了4块电池

详细：

1. 根据电池工业标准可知，充电电池、电瓶的“终止充电电压”为额定电压的1.2倍，就是
   说充电电池、电瓶充电到额定电压的1.2倍就充满了。

2. 48V充电器标准输出电压，应该是终止充电电压：48V×1.2＝57.6V。电动车48V充电器标
   准输出电压为55-55.9V之间显然是低了。

3. 充电电池、电瓶最忌讳三点：①、过度充电；②、过度放电；③、充电不足。......充
   电不足容易使电瓶极板硫化而损坏。所以充电器的好坏，直接影响电瓶使用寿命的。

4. 48V充电器，最高电压不大于59.6V，大于此电压，充电可能不转灯，低电压不低于55V，
   低于此电压造成充电不足，长时间容易对电池亏电，电流，如48V20A充电器，最大电流
   不大于3A。大于3A可能造成电池失水较早，最低不低于2.1A。低压此电流造成充电不足
   。

5. 48V新电池要求充电器参数，最高电压58.5---59.7，不低于58V，低于58V造成充电不足
   ，高 于59.7V可能造成充电不转灯。转灯电流约0.4---0.7A，实际电压约55.5V，低于
   50V造成充电不足，长时间充电电池亏电


一个单格铅酸电池的标称电压是2.0V,能放电到1.5V,能充电到2.4V；在应用中，经常用6个
单格铅酸电池串联起来组成标称是12V的铅酸电池，还有24V、36V、48V等。
2.0 x 6 x 4 = 48
1.5 x 6 x 4 = 36
2.4 x 6 x 4 = 57.6

铅电池 单体电压是10.5-14.5 伏
乘以 4  就是42-58伏

一般的48伏铅酸充电器电压是59伏，电池充满后会慢慢下降到56伏左右。

静止2小时后52.6伏左右，这个电池就是很好。电池用久了，2小时后52伏就不错了4块电池
电压也不一样了





上翻看上面的同学，回答的都是充电时的电压，楼主想问的是满电静置后的电压啊。
新电池满电的静置电压是13.2－13.5V之间，在这个范围以外的都有问题。

## find

```
➜  ~ find . -name "tmux.conf" -maxdepth 1
find: warning: you have specified the -maxdepth option after a non-option argument -name, but options are not positional (-maxdepth affects tests specified before it as well as those specified after it).  Please specify options before other arguments.
```

## python

```
>>> a,*b,c = [1,2,3,4,5,6]
>>> a
1
>>> b
[2, 3, 4, 5]
>>> c
6

>>> _,d,_ = (1,2,3)
>>> d
2
>>> _
3

>>> for a,b,c in [(1,2,3),(4,5,6),(7,8,9)]:
...     print(a)
...     print(b)
...     print(c)
...
1
2
3
4
5
6
7
8
9

>>> for tag,*args in [('a',1,2),('b',3,4),('c',5,6)]:
...     print(tag)
...     print(args)
...
a
[1, 2]
b
[3, 4]
c
[5, 6]


>>> line = 'nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false'
>>> uname, *fields, homedir, sh = line.split(':')
>>> uname
'nobody'
>>> homedir
'/var/empty'
>>> sh
'/usr/bin/false'
>>> fields
['*', '-2', '-2', 'Unprivileged User']
```

???用分割语法巧妙的实现递归算法
```
>>> def sum(items):
... head, *tail = items
... return head + sum(tail) if tail else head
...
>>> sum(items)
36
```

## DC/EP

DC，digital currency，数字货币
EP，electronic payment，电子支付

## 开关电源

https://www.bilibili.com/video/av14253527

### 早先老式的线性电源

先通过变压器线圈降压，如 220v 到 18v
然后通过四个二极管的桥式整流成直流
然后再串联一个降压电阻，降压到需要的电压 + 0.6v，如 12.6v
把12.6v电压连接到三极管的基极，则集电极得到需要的电压 12v

缺点：不稳定，因为 220v 的市电不稳定，所以降压后的 18v 也不稳定，则三极管的基极
的 12.6v 也不稳定，则集电极电压不稳

解决方法1：最简单的就是在基极连一个值为 12.65 的稳压二极管，但稳压二极管的稳压范
围特别窄，因此这种电源的稳压范围特别小。

解决方法2：基极连一个三极管，三极管发射极接地(基总)，三极管基极接几个电阻(称为取
样电阻)，称为串联降压型稳压电源

基本都已被开关电源替代，因纹波大。

像水波一样，纹波个数越多，纹波越小，纹波个数越少，纹波越大，220v50hz的市电，经上
面电路整流后的18v直流的纹波是每秒100个纹波，最后的12v的纹波也大，负载工作不稳定
。

也因此 18v 直流的并联滤波电容需要特别大，否则滤波不干净。最终的 12v 还要再接一个
滤波电容以过滤纹波。但越大的滤波电容在充电放电时，造成的电流损耗越大，从而造成带
动负载能力的降低。

缺点：纹波大，功率小，带负载能力弱

### 现在的开关电源

开关电源的四步：

1. 220v交流变311v直流：220v的电流经过一系列的抗干扰电路，通到整流桥，整流滤波后
   得到300v左右直流(因220v不稳定，这里的300v实际也不是稳定的300v，标准的话应该是
   311v)，但因这时的纹波特别大(每秒100个)，而并联的滤波电容又不能特别大(影响带载
   能力)，这时进入第二步。

2. 再把 300v 左右的直流电通过开关管、振荡器、储能电感逆变为交流电，然后再通过二
   极管整流为直流电。直流再重新转换为交流的目的是要得到高频的交流电(市电50HZ的频
   率太低)，因为频率越高，纹波越小，则高频交流再次整流后得到的直流纹波就小很多，
   则需要并联的滤波电容就小很多，则功率就可以增大，提高了带载能力。

3. 但由于 220v 的不稳定，导致其后的直流 300v 也不稳定，输出的最终直流电压也不稳
   定，因此还需要稳压，即 220v 电压无论怎么波动，最终输出的直流电压不波动。怎么
   做到稳压，先来看电路是怎么决定了最终的输出直流电压的?最终直流电压往上一步是由
   二次整流的次级线圈电压决定，而次级线圈电压由二次整流的初级线圈电压决定，而初
   级线圈电压由开关管的通断时间决定，而开关管的通断时间是由振荡器的开关信号确定
   的，振荡器实际就是负责调制脉冲宽度(PWM)，脉冲宽度增大，则开信号延长，最终输出
   电压升高，这就是最终输出电压的决定机制。因此必须要有个取样电路告诉振荡器当前
   输出的电压比需求的电压是高了还是低了，振荡器才能决定脉冲宽度是增大还是减小。
   反之如果没有反馈电路给振荡器，因为振荡器的电压来自初次整流后的 300v 电压再串
   接几个分压电阻，220v 的不稳定导致振荡器的电压不稳定，则振荡器的脉冲调制宽度也
   会随之变化，则最终输出电压就随市电的变化而变化。

   光耦(光电耦合器)，起到隔离作用。通过里面的发光二极管先把电转为光，光的强弱再
   影响三极管?的导通量，从而影响电压的高低，从而实现光再转换为电信号，参见
   https://www.bilibili.com/video/av14310233?t=161

   开关电源的稳压电路：稳高不稳低，即 220v 升高后，可以稳压，但 220v 降低后，稳
   压电路是不稳压的。

4. 增加加过压、过流保护电路：防止如稳压电路损坏等情况造成电压的忽然升高损坏负载。


能够起到隔离作用的元器件：
1. 变压器，通过电磁转换实现隔离
2. 光耦，通过光电转换实现隔离
3. 电容，不通直流


逆变：
直流电可以通过电阻实现直接降压，但现在仍然不能实现直流电的直接升压，即直流300v直
接升压为直流400v，目前没有电路、器件可以做到，但交流电可以方便的通过变压器降压和
升压，所以逆变的用途还可以用于升压直流电。

实现逆变的条件：
1. 必须有直流电输入
2. 必须有个电感，这里称为储能电感
3. 必须有个开关管(可能是三极管、场效应管、可控硅等，必须工作在开关状态，即导通和
   截至两个状态切换)
4. 必须有信号给到开关管，振荡器就负责给开关管传递开关信号，实现开关管的开关。
所以就是，振荡器产生开关信号，开关管根据信号进行开关，则电感处就会产生交流电。

### TL431 集成块

封装了比较放大器和三极管，有三个端 R,K,A，R 端输入，K 端输出，A 端接地，特点是输
入端 R 轻微升高，则输出端 K 成倍成倍的下降。

比较是和基准比较，即 A 端接地为基准，R 端输入和其比较，R 轻微升高，则输出端 K 成
倍成倍的下降。

稳压电路就是用取样电路和基准电路比较以后，把放大的结果通知振荡器。振荡器中的控制
器根据通知改变脉冲宽度，从而改变输出电压高低。

### 桥式整流

头接头直流输出
尾接尾接地
头尾相接交流输入

 ---220v----
           |
    ----|>----|>---- + 
    |
   GND
    |
    ----|>----|>---- -
           |
 ---220v----


### 三极管状态

* 截至(断路不通)
* 放大(即线性，相当于电阻)
* 饱和(相当于导线)

## 电解电容的防爆纹

电解电容器大容量的具有防爆阀，和电池的机制相似

电解电容的防爆纹有十字、K字、Y字的,这有什么区别吗?

是否表示防爆等级的不同,有什么规定吗?

以下是我google的,希望对你有帮助. 

1 "防爆"不是"不會爆",劃+字就是要讓它爆 

2 那有防爆 跟沒防爆 差在那? 
  答:有防爆是防止它蓄積太大的能量炸開,會將人炸傷,所以防爆其實是讓他比較容易被撐
開,不會蓄積太大能量,炸開時像炸藥一樣炸傷人..


"Y""K""+"各种形状,只是铝电解电容器防爆阀不同的形式,是机械冲压而成,都是一个目的,
防止电容器由于品质不良或其他原因瞬间爆炸之前,内部积蓄的能量能够从防爆槽处得到压
力释放,因为,铝外壳底部防爆槽加工完成后,直径在8mm以下的,压力释放值为
,0.35MPa,10-13mm的压力释放值为0.22MPa,16mm以上的为1.7MPa,最完美的防暴槽制作是为
点释放方式,如果电容器因为内部失效而造成爆炸前,积蓄的爆炸能量瞬间就从防爆槽某一点
进行点压力释放,从而避免失效电容器剧烈的爆炸而造成其他不良的后果,这样的方式不仅避
免污染线路板其他元件,而且也能避免对人体造成伤害,本公司生产的铝电解电容器,已经全
部达到点释放防爆.
作者：失传技术研究所工作室
https://www.bilibili.com/read/cv212910/
出处： bilibili

## 液晶显示器 LCD 

当 LCD 的白色背光点亮之后，有的屏幕像素是默认透光的，像素要通电后才不透光，称为
长白屏，反之有的屏幕默认是不透光的，像素通电后才透光，则称为长黑屏。

因显示器用于显示白色的时候很多，所以多用长白屏，即不加电就显示为白色，比较省电。

长黑屏：常用于电视，屏幕后面标注为 NB
长白屏：常用于显示器，屏幕后面标注为 WB

## 三极管

分为：
* 双极性三极管(常见的普通三极管)
* 单极型三极管(也称为场效应管、MOS管)

作用：
* 放大电流信号
* 开关

NPN 和 PNP 都是 EBC 三个极，分别是发射极、基极、集电极，电路符号中有箭头的都是表
示 E 极，两种三极管的发射极电流都是基极和集电极电流之和，不同是：
* NPN 型半导体的电路符号中，箭头指向三极管外部，因为 NPN 电流是从 B、C 极流向 E
  极
* PNP 型半导体的电路符号中，箭头指向三极管内部，因为 PNP 电流是从 E 极流向 B、C
  极

PNP 是用于控制电源的，NPN 是控制地

NPN 三极管示例图两个 N 型半导体都是同样大小，也就是感觉哪端做 C 极或做 E 极都可
以，但实际上两个极的大小是有区别的，不是对称大小，C 极比 E 极要大

Ic = beta x Ib
beta 约为 100

三极管的状态：
* 放大状态：发射结正偏，集电结反偏
* 截至状态：发射结反偏，集电结反偏
* 饱和状态：发射结正偏，集电结正偏

## 二极管

二极管的反向偏置时有 3 种状态：
* 反向截至
* 反向击穿，但可以通过电流
* 反向热击穿，二极管损坏

## 共模干扰和差模干扰

共模干扰
输入电路和输出电路同时产生的幅度相同、相位相同的对地干扰，可以用 Y 电容或共模电
感(又称为共模扼流圈)滤除共模干扰

差模干扰
输入电路和输出电路之间同时互相产生的幅度相同、相位相反的相互干扰，可以用 X 电容
或差模电感(又称为差模扼流圈)滤除差模干扰

## 安规电容，X电容，Y电容

安规电容分为 X 电容和 Y 电容，分别用于滤除差模干扰和共模干扰，和普通电容最大差别
是：普通电容击穿后是断路，而安规电容击穿后是通路。

X电容是安规电容的一种，根据IEC 60384-14，电容器分为X电容及Y电容，火线零线间的是X
电容。X电容用在电源滤波器里，起到电源滤波作用，对差模干扰起滤波作用。

Y电容用来消除共模干扰。也是分别跨接在电力线两线和地之间(L-E，N-E)的电容，一般是
成对出现。基于漏电流的限制，Y电容值不能太大。

X电容Y电容都是安全电容，区别是X电容接在输入线两端用来消除差模干扰，Y电容接在输入
线和地线之间，用来消除共模干扰。

X电容---金属薄膜电容器;通常，X电容多选用耐纹波电流比较大的聚脂薄膜类电容。这种类
型的电容，体积较大，但其允许瞬间充放电的电流也很大，而其内阻相应较小。X电容采用
塑封的方形高压cbb电容，cbb电容不但有更好的电气性能，而且与电源的输入端并联可以有
效的减小高频脉冲对电源的影响。

Y电容---常采用高压瓷片的。Y型电容连接在相线与地线之间。为了不超过相关安全标准限
定的地线允许泄漏值，这些电容的值大约在几nF。一般地，Y电容应连接到噪声干扰较大的
导线上。Y1属于双绝缘Y电容，用于跨接一二次侧。Y2则属于基本单绝缘Y电容，用于跨接一
次侧对地保护即FG线。

X电容和Y电容应用在开关电源中各种作用都不同，都是不可以互相替换的。

开关电源中电容的技术参数主要有电容量、耐压、损耗角、稳定性等。

### X 电容 

X电容多选用耐纹波电流比较大的聚脂薄膜类电容。这种类型的电容,体积较大,但其允许瞬
间充放电的电流也很大,而其内阻相应较小。 

X电容容值选取是μF级，此时必须在X电容的两端并联一个安全电阻,用于防止电源线拔掉时
,由于该电容被充电荷没泄放而致电源线插头长时间带电。安全标准规定,当正在工作之中的
机器电源线被拔掉时,在两秒钟内,电源线插头两端带电的电压(或对地电位)必须小于原来额
定工作电压的30%。

作为安全电容之一的X电容,也要求必须取得安全检测机构的认证。X电容一般都标有安全认
证标志和耐压AC250V或AC275V字样,但其真正的直流耐压高达2000V以上,使用的时候不要随
意使用标称耐压AC250V或者DC400V之类的普通电容来代用。

### Y 电容

单相交流电源输入分为3个端子：火线（L）/零线（N）/地线（G）。在火线和地线之间以及
在零线和地线之间并接的电容, 这两个Y电容连接的位臵比较关键,必须要符合相关安全标准
, 以防引起电子设备漏电或机壳带电,容易危及人身安全及生命。它们都属于安全电容,从而
要求电容值不能偏大,而耐压必须较高。

Y电容主要用于抑制共模干扰 

Y电容的存在使得开关电源有一项漏电流的电性能指标。

工作在亚热带的机器,要求对地漏电电流不能超过0.7mA;工作在温带的机器,要求对地漏电电
流不能超过0.35mA。因此,Y电容的总容量一般都不能超过4700PF（472）。

## 电感

电容：
通交流隔直流

电感：
通直流隔交流，通过其的电流不能突变，因此其电压和电流有相位差，电压领先于电流 90
度，作用是：阻流()、滤波(共模电感)、振荡(RLC 串联谐振电路)、选频(收音机)

## A, ah, mah, wh, C ratings

A(amps) 是电流的单位，1A 表示电路中的某个点每秒通过的电子数是 6.24*10e18 个

Ah(amp-hours) 是用来衡量电池供电能力的，如电池容量 2000mah，表示 2000ma 即 2A 的
电流供电，可以供电 1 个小时，或 1A 的电流供电 2 个小时，可以简单的根据电流估算电
池的供电时间，因为上述都是理想情况下，即不考虑电池内阻会把部分电量转化为热量的情
况下。

Wh(watt-hours)
Volts * Amp-hours = Watt-hours
(V)   * Ah        = Wh
也是用来衡量电池供电能力的，因为上面的 Ah 只考虑了电流，适用于衡量固定电压的电量
，如都是 1.2v 的两个品牌电池.......但考虑电流相同电压不同的情况时，如一节 1.2v
的 2000mah 电池，和 8 节串联的同型号电池，则其电量应分别为：
1.2v * 2ah = 2.4wh
(1.2v * 8) * 2ah = 19.2wh

C ratings
C 表示 capacity，是一种描述电池最大放电能力的非正式的方法，表示电池可以安全的输
出多大的电流，如一节电池表明 2000mah，则 4C 表示此电池最大可以 4 倍率安全放电，
即 4*2000mah = 8000ma = 8A，注意这里把 h 去掉了，所以这是一种非正式的方法，只是
让你有个概念，这个电池最大的放大电流可以有多大

## 电路分析三大方法

* 等效和替代
* 电路方程
* 线性叠加

## UIW

U = dW/dq 即电压表示单位电荷所携带的能量
I = dq/dt 即电流表示单位时间内通过某点的电荷量
W = dW/dt 即点位时间的能量

W = dW/dt = dW/dq * dq/dt = UI

## 电路板标识

RF  温度保险丝

FR  快恢复二极管，反向击穿电压较高，一般为 200v 以上，开关较快为几百纳秒

SBD 肖特基二极管，为两个二极管公用一个阴极，称为共阴极二极管，用于低电压大电流电
路，开关更快，只有几纳秒，反向击穿电压一般为 100v 一下

## PFC

先了解功率因数：
功率因数指的是有效功率与总耗电量（视在功率）之间的关系，也就是有效功率除以总耗电
量（视在功率）的比值。基本上功率因素可以衡量电力被有效利用的程度，当功率因素值越
大，代表其电力利用率越高。

PFC的英文全称为“PowerFactorCorrecTIon”，意思是“功率因数校正”，作用是对输入电
流波形进行控制，使其同步输入电压波形。

开关电源是一种电容输入型电路，其电流和电压之间的相位差会造成交换功率的损失，此时
便需要PFC电路提高功率因数。目前的PFC有两种，一种为被动式PFC（也称无源PFC）和主动
式PFC（也称有源式PFC）。


