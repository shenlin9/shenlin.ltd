---
title: Cmder
categories:
  - Windows
tags:
  - Windows
  - Cmder
---

可使用 cmder 代替 Windows 的命令行窗口，cmder 支持多种 shell，如 Git, PowerShell, BaShell 等。

<!--more-->

![](https://camo.githubusercontent.com/812b2647d6cd216ecddfb3f0ec71639473717955/687474703a2f2f692e696d6775722e636f6d2f67316e4e6630492e706e67)

## 安装运行

安装

    从官网下载 http://cmder.net/ 解压即可，绿色软件

运行

    把 cmder 的目录加入环境变量PATH

    Win+r 输入 "cmder"

右键菜单中添加"cmder here"

    在管理员权限的命令行中执行命令: Cmder /REGISTER ALL

## 快捷键

    新建 Tab : Ctrl+t ，有很多选项的对话框，包括以管理员身份运行

    关闭 Tab : Ctrl+w

    切换 Tab : Ctrl+Tab

    命令搜索 : Ctrl+r

    文本复制 ：Shift + mouse 左键，拖动选择完毕后释放左键时就复制到剪贴板了

               测试直接 mouse 左键也可以

    矩形复制 ：Alt + mouse 左键，选择范围是一个矩形框
    
        粘贴 : mouse 右键单击

    设置选项 : Win+Alt+p

    跳到上级目录 : ctrl+alt+u

    删光标前字符 : ctrl+u(linux 风格)  ctrl+home(windows 风格)
    
    删光标后字符 : ctrl+k(linux 风格)  ctrl+end(windows 风格)


## shell 种类

cmder 支持多种 shell，新建 Tab 时，可选择 shell 的种类：

|Task|Shell|Description|
|---|---|---|
|Cmder |cmd.exe |Windows 'cmd.exe' shell enhanced with Git, Git aware prompt, Clink(GNU Readline), and Aliases.|
|Cmder as Admin |cmd.exe |Administrative Windows 'cmd.exe' Cmder shell.|
|PowerShell |powershell.exe |Windows PowerShell enhanced with Git and Git aware prompt .|
|PowerShell as Admin |powershell.exe |Administrative Windows 'powershell.exe' Cmder shell.|
|Bash |bash.exe |Unix/Linux like bash shell running on Windows.|
|Bash as Admin |bash.exe |Administrative Unix/Linux like bash shell running on Windows.|
|Mintty |bash.exe |Unix/Linux like bash shell running on Windows. See below for Mintty configuration differences|
|Mintty as Admin |bash.exe |Administrative Unix/Linux like bash shell running on Windows. See below for Mintty configuration differences|

详细信息：https://github.com/cmderdev/cmder

## Q&amp;A

* `ls` 中文显示为`??`号

    打开`windows/system32/cmd.exe`文件属性，选择“使用旧版控制台”

* `ls`时中文文件名乱码

    打开设置面板，找到Startup -> Envrioment选项，

    在下面的文本框里添加

    ```
    set LANG=zh_CN.UTF-8
    ```

* 使用上下方向键切换历史命令时总是莫名其妙多出字符

    和默认提示字符 `λ` 有关，打开文件 `cmder/vendor/clink.lua`

    找到下列行，将字符 `λ` 替换为 `$` 即可

    ```
    if env == nil then
        lambda = "λ"
    else
        lambda = "("..env..") λ"
    end
    ```
* 看不到命令提示符 `λ` 或 `$`

    是因为命令提示符的颜色和背景色都设为了黑色，打开文件 `cmder/vendor/clink.lua`

    找到 41 行附近的 `local cmder_prompt = "\x1b[1;32;40m{cwd} {git}{hg}{svn} \n\x1b[1;32;40m{lamb} \x1b[0m"`

    把 `[1;32;40m{lamb}` 中的 32 改为 37

* 命令行里有中文字符时，就可以删除到底把提示符 `$`  都删了

    打开`settings --> main --> 选择Alternative font`，设置字体为黑体，Unicode ranges 为CJK，然后点击 apply

* 按 TAB 命令补全时，把路径中的斜线 `/` 替换为了反斜线 `\\`，结果显示文件不存在

    未解决
