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
