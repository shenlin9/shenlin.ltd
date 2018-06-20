---
title: Linux - systemd 命令 - systemctl, systemd-analyze, hostnamectl, localectl, timedatectl, loginctl
categories:
  - Linux
tags:
  - Linux
  - systemd
  - systemctl
  - systemd-analyze
  - hostnamectl
  - localectl
  - timedatectl
  - loginctl
---

systemd

<!--more-->

systemctl list-units命令可以查看当前系统的所有 Unit 。


    # 列出正在运行的 Unit
    $ systemctl list-units

    # 列出所有Unit，包括没有找到配置文件的或者启动失败的
    $ systemctl list-units --all

    # 列出所有没有运行的 Unit
    $ systemctl list-units --all --state=inactive

    # 列出所有加载失败的 Unit
    $ systemctl list-units --failed

    # 列出所有正在运行的、类型为 service 的 Unit
    $ systemctl list-units --type=service

4.2 Unit 的状态

systemctl status命令用于查看系统状态和单个 Unit 的状态。


    # 显示系统状态
    $ systemctl status

    # 显示单个 Unit 的状态
    $ sysystemctl status bluetooth.service

    # 显示远程主机的某个 Unit 的状态
    $ systemctl -H root@rhel7.example.com status httpd.service

除了status命令，systemctl还提供了三个查询状态的简单方法，主要供脚本内部的判断语句使用。


    # 显示某个 Unit 是否正在运行
    $ systemctl is-active application.service

    # 显示某个 Unit 是否处于启动失败状态
    $ systemctl is-failed application.service

    # 显示某个 Unit 服务是否建立了启动链接
    $ systemctl is-enabled application.service

4.3 Unit 管理

对于用户来说，最常用的是下面这些命令，用于启动和停止 Unit（主要是 service）。


    # 立即启动一个服务
    $ sudo systemctl start apache.service

    # 立即停止一个服务
    $ sudo systemctl stop apache.service

    # 重启一个服务
    $ sudo systemctl restart apache.service

    # 杀死一个服务的所有子进程
    $ sudo systemctl kill apache.service

    # 重新加载一个服务的配置文件
    $ sudo systemctl reload apache.service

    # 重载所有修改过的配置文件
    $ sudo systemctl daemon-reload

    # 显示某个 Unit 的所有底层参数
    $ systemctl show httpd.service

    # 显示某个 Unit 的指定属性的值
    $ systemctl show -p CPUShares httpd.service

    # 设置某个 Unit 的指定属性
    $ sudo systemctl set-property httpd.service CPUShares=500

4.4 依赖关系

Unit 之间存在依赖关系：A 依赖于 B，就意味着 Systemd 在启动 A 的时候，同时会去启动 B。

systemctl list-dependencies命令列出一个 Unit 的所有依赖。

    $ systemctl list-dependencies nginx.service

上面命令的输出结果之中，有些依赖是 Target 类型（详见下文），默认不会展开显示。如果要展开 Target，就需要使用--all参数。

    $ systemctl list-dependencies --all nginx.service


### 目标

systemd 用目标（target）替代了运行级别的概念，提供了更大的灵活性，如您可以继承一个已有的目标，并添加其它服务，来创建自己的目标。下表列举了 systemd 下的目标和常见 runlevel 的对应关系：

|命令|功能|
|---|---|
|sudo systemctl list-units --type=target|查询当前target|
|sudo systemctl isolate graphical.target|改变当前target，重启无效|
|sudo systemctl enable multi-user.target|改变启动时默认target|
|sudo systemctl enable kdm.service|graphical是默认target，指定使用的display manager|

**Sysvinit 运行级别和 systemd 目标的对应表**

|Sysvinit 运行级别|Systemd 目标|备注|
|---|---|---|
|0 |runlevel0.target, poweroff.target |关闭系统。|
|1, s, single |runlevel1.target, rescue.target |单用户模式。|
|2, 4 |runlevel2.target, runlevel4.target, multi-user.target |用户定义/域特定运行级别。默认等同于 3。|
|3 |runlevel3.target, multi-user.target |多用户，非图形化。用户可以通过多个控制台或网络登录。|
|5 |runlevel5.target, graphical.target |多用户，图形化。通常为所有运行级别 3 的服务外加图形化登录。|
|6 |runlevel6.target, reboot.target |重启|
|emergency |emergency.target |紧急 Shell|


## Systemd 管理

systemd 的主要命令行工具是 systemctl。

多数管理员应该都已经非常熟悉系统服务和 init 系统的管理，比如 service、chkconfig 以及 telinit 命令的使用。

systemd 也完成同样的管理任务，只是命令工具 systemctl 的语法有所不同而已，因此用表格来对比 systemctl 和传统的系统管理命令会非常清晰。

**systemd服务管理**

|命令|功能|
|---|---|
|sudo systemctl is-enabled .service|查询服务是否开机启动|
|sudo systemctl enable .service|开机运行服务|
|sudo systemctl disable .service|取消开机运行|
|sudo systemctl start .service|启动服务|
|sudo systemctl stop .service|停止服务|
|sudo systemctl restart .service|重启服务|
|sudo systemctl reload .service|重新加载服务配置文件|
|sudo systemctl status .service|查询服务运行状态|
|sudo systemctl --failed|显示启动失败的服务|

**Systemd 命令和 sysvinit 命令的对照表**

|Sysvinit 命令|Systemd 命令|备注|
|---|---|---|
|service foo start|systemctl start foo.service|用来启动一个服务 (并不会重启现有的)|
|service foo stop|systemctl stop foo.service|用来停止一个服务 (并不会重启现有的)。|
|service foo restart|systemctl restart foo.service|用来停止并启动一个服务。|
|service foo reload|systemctl reload foo.service|当支持时，重新装载配置文件而不中断等待操作。|
|service foo condrestart|systemctl condrestart foo.service|如果服务正在运行那么重启它。|
|service foo status|systemctl status foo.service|汇报服务是否正在运行。|
|ls /etc/rc.d/init.d/|systemctl list-unit-files --type=service|用来列出可以启动或停止的服务列表。|
|chkconfig foo on|systemctl enable foo.service|在下次启动时或满足其他触发条件时设置服务为启用|
|chkconfig foo off|systemctl disable foo.service|在下次启动时或满足其他触发条件时设置服务为禁用|
|chkconfig foo|systemctl is-enabled foo.service|用来检查一个服务在当前环境下被配置为启用还是禁用。|
|chkconfig –list|systemctl list-unit-files --type=service|输出在各个运行级别下服务的启用和禁用情况|
|chkconfig foo –list|ls /etc/systemd/system/*.wants/foo.service|用来列出该服务在哪些运行级别下启用和禁用。|
|chkconfig foo –add|systemctl daemon-reload|当您创建新服务文件或者变更设置时使用。|
|telinit 3|systemctl isolate multi-user.target (OR systemctl isolate runlevel3.target OR telinit 3)|改变至多用户运行级别。|


**systemd 电源管理命令**

|命令|操作|
|---|---|
|systemctl reboot |重启机器|
|systemctl poweroff |关机|
|systemctl suspend |待机|
|systemctl hibernate |休眠|
|systemctl hybrid-sleep |混合休眠模式（同时休眠到硬盘并待机）|

关机不是每个登录用户在任何情况下都可以执行的，一般只有管理员才可以关机。
正常情况下系统不应该允许 SSH 远程登录的用户执行关机命令。否则其他用户正在工作，一个用户把系统关了就不好了。
为了解决这个问题，传统的 Linux 系统使用 ConsoleKit 跟踪用户登录情况，并决定是否赋予其关机的权限。
现在 ConsoleKit 已经被 systemd 的 logind 所替代。

logind 不是 pid-1 的 init 进程。它的作用和 UpStart 的 session init 类似，但功能要丰富很多，它能够管理几乎所有用户会话(session)相关的事情。logind 不仅是 ConsoleKit 的替代，它可以：

    维护，跟踪会话和用户登录情况。如上所述，为了决定关机命令是否可行，系统需要了解当前用户登录情况，如果用户从 SSH 登录，不允许其执行关机命令；如果普通用户从本地登录，且该用户是系统中的唯一会话，则允许其执行关机命令；这些判断都需要 logind 维护所有的用户会话和登录情况。
    Logind 也负责统计用户会话是否长时间没有操作，可以执行休眠/关机等相应操作。
    为用户会话的所有进程创建 CGroup。这不仅方便统计所有用户会话的相关进程，也可以实现会话级别的系统资源控制。
    负责电源管理的组合键处理，比如用户按下电源键，将系统切换至睡眠状态。
    多席位(multi-seat) 管理。如今的电脑，即便一台笔记本电脑，也完全可以提供多人同时使用的计算能力。多席位就是一台电脑主机管理多个外设，比如两个屏幕和两个鼠标/键盘。席位一使用屏幕 1 和键盘 1；席位二使用屏幕 2 和键盘 2，但他们都共享一台主机。用户会话可以自由在多个席位之间切换。或者当插入新的键盘，屏幕等物理外设时，自动启动 gdm 用户登录界面等。所有这些都是多席位管理的内容。ConsoleKit 始终没有实现这个功能，systemd 的 logind 能够支持多席位。

以上描述的这些管理功能仅仅是 systemd 的部分功能，除此之外，systemd 还负责系统其他的管理配置，比如配置网络，Locale 管理，管理系统内核模块加载等，完整地描述它们已经超出了本人的能力。

```bash
# 查看版本
$ systemctl --version
```

## Systemd 命令

Systemd 并不是一个命令，而是一组命令，涉及到系统管理的方方面面。
3.1 systemctl

systemctl是 Systemd 的主命令，用于管理系统。


    # 重启系统
    $ sudo systemctl reboot

    # 关闭系统，切断电源
    $ sudo systemctl poweroff

    # CPU停止工作
    $ sudo systemctl halt

    # 暂停系统
    $ sudo systemctl suspend

    # 让系统进入冬眠状态
    $ sudo systemctl hibernate

    # 让系统进入交互式休眠状态
    $ sudo systemctl hybrid-sleep

    # 启动进入救援状态（单用户状态）
    $ sudo systemctl rescue

3.2 systemd-analyze

systemd-analyze命令用于查看启动耗时。


    # 查看启动耗时
    $ systemd-analyze                                                                                       

    # 查看每个服务的启动耗时
    $ systemd-analyze blame

    # 显示瀑布状的启动过程流
    $ systemd-analyze critical-chain

    # 显示指定服务的启动流
    $ systemd-analyze critical-chain atd.service

3.3 hostnamectl

hostnamectl命令用于查看当前主机的信息。


    # 显示当前主机的信息
    $ hostnamectl

    # 设置主机名。
    $ sudo hostnamectl set-hostname rhel7

3.4 localectl

localectl命令用于查看本地化设置。


    # 查看本地化设置
    $ localectl

    # 设置本地化参数。
    $ sudo localectl set-locale LANG=en_GB.utf8
    $ sudo localectl set-keymap en_GB

3.5 timedatectl

timedatectl命令用于查看当前时区设置。


    # 查看当前时区设置
    $ timedatectl

    # 显示所有可用的时区
    $ timedatectl list-timezones                                                                                   

    # 设置当前时区
    $ sudo timedatectl set-timezone America/New_York
    $ sudo timedatectl set-time YYYY-MM-DD
    $ sudo timedatectl set-time HH:MM:SS

3.6 loginctl

loginctl命令用于查看当前登录的用户。


    # 列出当前session
    $ loginctl list-sessions

    # 列出当前登录用户
    $ loginctl list-users

    # 列出显示指定用户的信息
    $ loginctl show-user ruanyf

systemctl命令在enable、disable和mask子命令中增加了--now选项，可以实现激活的同时启动服务，取消激活的同时停止服务。
```
[shenlin@t460p /usr/local/src]$ systemctl enable --now mysqld.service
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-unit-files ===
Authentication is required to manage system service or unit files.
Authenticating as: shenlin
Password:
==== AUTHENTICATION COMPLETE ===

==== AUTHENTICATING FOR org.freedesktop.systemd1.reload-daemon ===
Authentication is required to reload the systemd state.
Authenticating as: shenlin
Password:
==== AUTHENTICATION COMPLETE ===

==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to manage system services or units.
Authenticating as: shenlin
Password:
==== AUTHENTICATION COMPLETE ===
```

## Temp

chkconfig
查询或设置系统服务的运行级别信息
实际操作的是这些儿文件夹：/etc/rc[0-6].d

字体名字前有@的是横向文字
