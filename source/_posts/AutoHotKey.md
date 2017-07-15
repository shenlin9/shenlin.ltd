# AutoHotKey

标签（空格分隔）： AAA autohotkey

---

; IMPORTANT INFO ABOUT GETTING STARTED: Lines that start with a
; semicolon, such as this one, are comments.  They are not executed.

; This script has a special filename and path because it is automatically
; launched when you run the program directly.  Also, any text file whose
; name ends in .ahk is associated with the program, which means that it
; can be launched simply by double-clicking it.  You can have as many .ahk
; files as you want, located in any folder.  You can also run more than
; one ahk file simultaneously and each will get its own tray icon.

; SAMPLE HOTKEYS: Below are two sample hotkeys.  The first is Win+Z and it
; launches a web site in the default browser.  The second is Control+Alt+N
; and it launches a new Notepad window (or activates an existing one).  To
; try out these hotkeys, run AutoHotkey again, which will load this file.

load()

load(){
    Sleep,15000
    ;Run,D:\WPS快盘\Manual\php_enhanced_zh.chm,,Max
    ;Sleep,500
    ;Run,D:\Documents\Desktop\CentOS_silence.bat,,Hide
    ;Sleep,500
    ;Run,C:\Program Files\NetSarang\Xshell 5\Xshell.exe,,Max
    ;Sleep,10000
    Run,C:\Program Files\Git\git-bash.exe --cd-to-home,,Max
    Sleep,5000
    Run,D:\install\cmd_markdown_win32\Cmd Markdown.exe,,Max
    Sleep,20000
    Run,https://git-scm.com/book/zh/v2,,Max
    Sleep,20000
    Run,http://rogerdudler.github.io/git-guide/index.zh.html,,Max
    Sleep,20000
    Run,https://www.laravist.com/remarks,,Max
    Sleep,20000
    WinActivate,ahk_class 360se6_Frame
    Click, 610, 629

    return
}


;----打开对隐藏窗口的探测------
;DetectHiddenWindows, On


;----一键开关音量------
F7::SoundSet, +1, , mute


;----360浏览器快捷键，因360自带的老板快捷键功能有切换后焦点不还原的问题------

^q::
{
    IfWinNotActive ahk_class 360se6_Frame
    {
        ;必须先显示再激活
        WinShow,ahk_class 360se6_Frame
        WinActivate,ahk_class 360se6_Frame
    }
    else
    {
        ;隐藏360浏览器窗口
        WinHide,ahk_class 360se6_Frame

        ;切换窗口
        Send !{Tab}
    }

    return  
}

;----有道词典的Mini窗口快捷键-------
Insert::
{

    Send ^!{m}
    return
}

Pause::
{

    showList()
    return
}


showList(){
    IfWinNotActive ahk_class YdMiniModeWndClassName
    {
        ;显示并把焦点定位到单词输入框窗口
        WinActivate,ahk_class YdMiniModeWndClassName

        ;显示单词内容窗口（只在有内容的时候显示，否则会显示出一个黑块）
        WinGetPos,,, Width, Height, ahk_class YdMiniCefWnd
        if (%Height% != 0)
            WinShow,ahk_class YdMiniCefWnd
    }
    else
    {
        ;隐藏单词内容窗口
        WinHide,ahk_class YdMiniCefWnd

        ;设置透明度
        ;WinSet, Transparent, 40, ahk_class YdMiniModeWndClassName

        ;切换窗口
        Send !{Tab}
    }
}


Enter::
{
    Send {Enter}
    IfWinActive ahk_class YdMiniModeWndClassName
    {
        ;根据图片找加入生词本按钮失败
        ;ImageSearch, FoundX, FoundY, 0, 0, 300, 300, *w *h *Trans0xF83636 C:\Program Files\Youdao\7.1.0.0421\skins\icons\qcstore-clicked.png

        ;记录光标原位置
        MouseGetPos, xpos, ypos

        ;激活内容窗体
        WinWait, ahk_class YdMiniCefWnd
        Sleep,500
        WinActivate,ahk_class YdMiniCefWnd

        ;根据颜色找到加入生词本的图片
        CoordMode, Pixel, Client
        PixelSearch, Px, Py, 20, 20, 234, 45, 0xF83636, 3, RGB
        if (!ErrorLevel)
        {
            ;设置鼠标移动速度
            SetDefaultMouseSpeed,0
            ;点击加入生词本图片
            Click, %Px% %Py%
            ;还原鼠标位置
            MouseMove, %xpos%, %ypos%

            ;设置焦点到输入框等待下一次输入
            ControlFocus, Edit1, ahk_class YdMiniModeWndClassName
            ;把因为焦点到输入框而隐藏的窗体显示出来
            WinShow,ahk_class YdMiniCefWnd
        }
    }
    return
}


;win+z
;-----------
#z::Run www.autohotkey.com 

;ctrl+alt+n
;-----------
^!n::   
IfWinExist Untitled - Notepad
	WinActivate
else
	Run Notepad
return


; Note: From now on whenever you run AutoHotkey directly, this script
; will be loaded.  So feel free to customize it to suit your needs.

; Please read the QUICK-START TUTORIAL near the top of the help file.
; It explains how to perform common automation tasks such as sending
; keystrokes and mouse clicks.  It also explains more about hotkeys.




