---
title: AutoHotKey - 杂项
categories:
  - AutoHotKey
tags:
  - AutoHotKey
---

AutoHotKey - 杂项

<!--more-->

## 检测一次、两次、还是三次按键

```autohotkey
; 示例 #3: 检测热键的单次, 两次和三次按下. 这样
; 允许热键根据您按下次数的多少
; 执行不同的操作：
F9::
if winc_presses > 0 ; SetTimer 已经启动, 所以我们记录键击.
{
    winc_presses += 1
    return
}
; 否则, 这是新开始系列中的首次按下. 把次数设为 1 并启动
; 计时器：
winc_presses = 1
SetTimer, KeyWinC, 400 ; 在 400 毫秒内等待更多的键击.
return

KeyWinC:
SetTimer, KeyWinC, off
if winc_presses = 1 ; 此键按下了一次.
{
    msgbox,1
}
else if winc_presses = 2 ; 此键按下了两次.
{
    msgbox,2
}
else if winc_presses > 2
{
    MsgBox, Three or more clicks detected.
}
; 不论触发了上面的哪个动作, 都对 count 进行重置
; 为下一个系列的按下做准备:
winc_presses = 0
return
```

## 遍历窗口

```autohotkey
;---遍历窗口----
;^9::show_all_win("ahk_class PotPlayer64")

show_all_win(win_title)
{
    DetectHiddenWindows,On
    WinGet, id, list, %win_title%
    Loop, %id%
    {
        this_id := id%A_Index%
        WinGetTitle, this_title, ahk_id %this_id%
        WinGetClass, this_class, ahk_id %this_id%
        MsgBox, 4, , Visiting All Windows`n%a_index% of %id%`nahk_id %this_id%`nahk_class %this_class%`n%this_title%`n`nContinue?
        IfMsgBox, NO, break

        ;if (this_title != "Video Editor Plus")
        ;    WinShow, ahk_id %this_id%
        ;else
        ;{
        ;    WinHide, ahk_id %this_id%
        ;    Send !{TAB}
        ;}
    }
}
```


## 杂项

```autohotkey
;---test，双击 win 键---
;RWin::
;if (A_PriorHotkey = "RWin" and A_TimeSincePriorHotkey < 400)
;{
;    send #r
;}
;return

;---test，右 win 键代替鼠标右键---
RWin::AppsKey

;---test，~ 符号表示不影响按键的原来功能 ---
~ScrollLock::msgbox,scroll lock ...


;读写注册表
RegWrite, REG_DWORD, HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\, ProxyEnable, %OutputVar%
RegRead, OutputVar, HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections\, DefaultConnectionSettings
RegWrite, REG_BINARY, HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections\, DefaultConnectionSettings, %Proxy%
msgbox,Done:%OutputVar%


;鼠标相关
WheelUp::msgbox,滚轮向上滚动
WheelDown::msgbox,滚轮向下滚动

;四向滚轮
WheelLeft::^+TAB
WheelRight::^TAB

;MButton::msgbox,滚轮点击
;MButton::
;SoundGet, master_mute, , mute
;SoundSet, +1, , mute
;if (master_mute = "On")
;{
;    ;系统音量调节
;    ;Run,%windir%\System32\SndVol.exe -f 49825268
;
;    ;右下角的 thinkpad 音量调节
;    ControlClick, x2145 y33, ahk_class Shell_TrayWnd
;}
;else
;{
;    SoundSet, 10
;}
;return

;XButton1::msgbox,四键鼠标
;XButton2::msgbox,五键鼠标
;XButton1::
;IfWinActive, ahk_class CabinetWClass
;    Send {Backspace}
;else
;    Send ^w
;return
;XButton2::^TAB
;XButton2::Backspace
;XButton2::^+TAB
```

## 锁定系统和更改睡眠时间

```autohotkey
RunWait, rundll32.exe user32.dll`,LockWorkStation    ; 要运行的目标程序中有逗号则必须被转义
RunWait, %comspec% /c powercfg /X -monitor-timeout-ac 2 && powercfg /X -standby-timeout-ac 2,,Hide
```

## 关闭、打开显示器

```autohotkey
SendMessage,0x112,0xF170,2,,Program Manager
            ;0x112：WM_SYSCOMMAND，
            ;0xF170：SC_MONITORPOWER，
                    ;2：关闭。
                    ;1：activate thedisplay "low power" mode。
                    ;-1：turn the monitor on
```

## 关闭、打开显示器并设定休眠时间

```autohotkey
;============================关闭、打开显示器并设定休眠时间====================
; Wait for it to be released because otherwise the hook state gets reset
; while the key is down, which causes the up-event to get suppressed,
; which in turn prevents toggling of the ScrollLock state/light:
~ScrollLock::
KeyWait, ScrollLock
GetKeyState, ScrollLockState, ScrollLock, T
If ScrollLockState = D
{
    RunWait, %comspec% /c powercfg /X -standby-timeout-ac 2,,Hide
    ;RunWait, %comspec% /c powercfg /X -monitor-timeout-ac 2 && powercfg /X -standby-timeout-ac 2,,Hide
    RunWait, rundll32.exe user32.dll`,LockWorkStation    ; 要运行的目标程序中有逗号则必须被转义
    Sleep,500
    SendMessage,0x112,0xF170,2,,Program Manager
} else {
    RunWait, %comspec% /c powercfg /X -standby-timeout-ac 60,,Hide
}
```

## 睡眠

```autohotkey
; 即使关闭了休眠，也不是真正的睡眠，是一种只能使用电源按键唤醒的混合方式
RunWait, %comspec% /c rundll32.exe powrprof.dll`,SetSuspendState 0`,1`,0
;BOOLEAN SetSuspendState(
;  BOOLEAN bHibernate,
    ;// If this parameter is TRUE, the system hibernates. If the parameter
    ;// is FALSE, the system is suspended.
;  BOOLEAN bForce
    ;//  This parameter has no effect.
;  BOOLEAN bWakeupEventsDisabled
    ;// If this parameter is TRUE, the system disables all wake events. If
    ;// the parameter is FALSE, any system wake events remain enabled.
;);

; 这个方法没测试
DllCall("PowrProf\SetSuspendState", "Int", 0, "Int", 0, "Int", 0)
```
https://superuser.com/questions/42124/how-can-i-put-the-computer-to-sleep-from-command-prompt-run-menu

## 按键序列

```autohotkey
; 先按左 Contrl 再输入两个字符后触发
LControl & F1::return  ; Make left-control a prefix by using it in front of "&" at least once.
LControl::
Input, OutputVar, L2
MsgBox %OutputVar%
return

; 按键序列，先按 
g & F1::return
g::
Input, OutputVar, L2
MsgBox %OutputVar%
return

; 同时按下 a 和 k
a & k::MsgBox You press a and k same time
$a::Send, a ;避免无法输入 a

; 按键序列，自动触发
; 输入 @shen 后自动替换为邮件地址
:*:@me::shenlin1970@gmail.com

; 按键序列，空格触发
::btw::
MsgBox, You input btw
return


;实现 Ctrl + e + c 这样的效果
;https://superuser.com/questions/724653/how-do-i-create-a-hotkey-for-a-sequence-of-inputs-while-holding-ctrl-e-g-ctrl/725303#725303
~$Ctrl UP::
    ChordIsBroken := True
    Return
^e::
    ChordIsBroken := False
    Input, OutputVar, L1 M
    If (!ChordIsBroken && Asc(OutputVar) = 3)
    {
        MsgBox "Hello, World!"
    }
    Else
    {
        SendInput %OutputVar%
    }
    Return

$^e::
```

速盘急速版
```autohotkey
; ================速盘急速版破解，自动更改日期和开始下载=================

; ------今天实际日期--------
Today = %A_YYYY%/%A_MM%/%A_DD%

; ------昨天的日期--------
Yesterday  = %A_Now%   ; 必须为 YYYYMMDDHH24MISS 格式才能使用下面的函数
EnvAdd, Yesterday, -1, days
FormatTime, Yesterday, %Yesterday%, ShortDate

TheDate = %Today%

; ------设置系统日期--------
SetDate()
{
    global

    if (TheDate = Today)
        TheDate = %Yesterday%
    else
        TheDate = %Today%

    RunWait, %comspec% /c date %TheDate%,,Hide
    return
}

; ------启动程序自动开始下载--------
DownLoad()
{
    SetDate()

    Run,supan
    WinWait,速盘 - 极速版
    Sleep,100
    Send {Click 650,123}
    Send {Click 1493,239}
    Send !{TAB}
    Sleep,70000

    IfWinExist,速盘 - 极速版
    {
        WinActivate
        WinClose
        Sleep,500
        IfWinExist, Confirm
            Send {Enter}
        Sleep,1500
        DownLoad()
    }
    return
}

;^\::DownLoad()
```

只按 RWin 键则弹出运行框，但和其他键组合则不弹运行对话框
Make RWin a prefix by using it in front of "&" at least once.
```autohotkey
RWin & F1::return
RWin::#r
```

双击ctrl
```autohotkey
;=============================test=========================================

~LControl::
if (A_PriorHotkey = "~LControl" and A_TimeSincePriorHotkey < 500)
{
    ; Too much time between presses, so this isn't a double-press.
    Send, #s
    return
}
```
