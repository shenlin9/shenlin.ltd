---
title: C 语言 - Linux C 编程一站式学习 chapter 10 - gdb
categories: 
    - C 语言
    - Linux C 编程一站式学习
tags: C 语言
---

gdb

<!-- more -->

编译时必须添加 `-g` 选项，生成的执行文件才可以使用 gdb 进行源码级调试
```
$ gcc -g test.c -o test
```
* `-g` 选项的作用是在生成的执行文件里加入源码信息，如执行文件里的第几条指令对应源代码文件第几行
* 并不是把源代码文件嵌入进了生成的执行文件，所以调试时是需要源代码文件的，gdb 也必须能找到源代码文件

启动 gdb
```
$ gdb

$ gdb test          //启动 gdb 并开始调试 test.exe 文件
```
* gdb 提供了类似 shell 的命令行环境
* gdb 环境提示符为 `(gdb)`


帮助
```
(gdb) help          //显示所有命令类别

(gdb) help all      //显示所有类别下的所有命令，超多

(gdb) help files    //显示 files 类别下的命令

(gdb) help list     //显示 list 命令的帮助
```

基本
```
(gdb) file test     //调试 test.exe 文件

(gdb) list          //列出源代码，一次列 10 行，也可简写为 l

(gdb)               //提示符下直接回车表示重复执行上一次的命令

(gdb) l 3           //从第 3 行开始列出源代码，一次列 10 行

(gdb) l add_range   //列出函数 add_range 相关代码，注意 add_range 必须是函数名，并不是输入变量也可以的模糊查找

(gdb) quit          //退出 gdb
```

## 单步执行
```
(gdb) start         //启动断点调试，将进入 main 函数中，并定位到变量定义后的第一条语句，表示这是即将执行的下一条语句，等待调试指令

(gdb) n             //next，一行代码一行代码的单步调试，不进入被调用的函数中

(gdb) s             //step，一行代码一行代码的单步调试，会进入被调用的函数中

(gdb) bt            //backtrace，显示函数调用的栈帧，包括了函数的调用关系和传递的参数
#0  add_range (from=1, to=10) at test.c:16  // add_range 位于 0 号栈帧
#1  0x00401482 in main () at test.c:7       // main 位于 1 号栈帧

(gdb) i locals      //info locals，查看函数局部变量的值

(gdb) f 1           //frame，选择 1 号栈帧，即 main 函数的栈帧，这时再执行 i locals 则查看的是 main 函数的局部变量

(gdb) p sum         //print，打印变量 sum 的值
$2 = 30349          //$2 是每次执行 print 命令时 gdb 保存的数据的编号

(gdb) finish        //让程序运行到从当前函数返回为止，当确认当前执行的函数没有错误则不必耗费时间一步步执行了

(gdb) set var sum = 0   //修改变量 sum 的值为 0，例如发现错误可能是因为 sum 的值导致的，可以在执行中修改它的值然后查看运行结果

(gdb) p sum = 0         //也可以用 print 命令给变量赋值
```

## 断点调试

断点是程序执行到某一代码行时中断，有助于快速跳过没有问题的代码。

```
(gdb) display sum       //跟踪显示 sum 的值，执行每一步都把 sum 的值显示出来
1: sum = 12123          //1 是 sum 的跟踪编号

(gdb) undisplay 1       //根据编号取消跟踪显示 sum 的值

(gdb) b 9               //break，在第 9 行设置断点

(gdb) b 9 if sum != 0   //break if，设置条件断点：满足指定条件断点才激活

(gdb) b add_range       //表示在函数 add_range 的开头设置断点

(gdb) c                 //continue，连续运行而非单步执行，遇断点则停止

(gdb) i breakpoints     //info，查看设置的所有断点

(gdb) i b               //info，查看设置的所有断点 breakpoints 和观察点 watchpoints

(gdb) delete  2        //删除 2 号断点

(gdb) delete breakpoints    //删除所有断点，会询问是否确认

(gdb) delete                //删除所有断点 ???需确认

(gdb) disable 2             //禁用 2 号断点

(gdb) enable 2              //启用 2 号断点

(gdb) r                     //run，从程序开头重新开始运行
```

## 观察点

观察点是当程序访问某个存储单元时中断，当不知道存储单元是在哪里被改动时使用

```
(gdb) x/7b arr              //打印指定存储单元 input 的内容，7b 是打印格式，b 表示每个字节一组，7 表示打印 7 组
0xbfb8f0a7: 0x31 0x32 0x33 0x34 0x35 0x00 0x00  //前面的是内存地址

(gdb) watch arr[3]          //设置观察点

(gdb) i watchpoints         //info，查看观察点列表
```

## 段错误

Segmentation fault

段错误发生的一种情况：

    如果函数的局部变量访问时越界了，有可能并不立即产生段错误，而是在函数返回时才发生段错误。

如果程序运行时发生段错误，可以在 gdb 中使用 'r' 命令运行，则发生段错误时 gdb 会自动停下来，并显示运行到哪一行代码了

```
$ gdb test

(gdb) start

(gdb) r
Program received signal SIGSEGV, Segmentation fault.
0xb7e1404b in _IO_vfscanf () from /lib/tls/i686/cmov/libc.so.6      //段错误发生在 _IO_vfscanf 函数，这是系统函数，所以应该是我们调用它时发生了错误

(gdb) bt                                                            //使用 bt 命令发现 _IO_vfscanf 函数是被我们的 scanf() 函数调用了
#0 0xb7e1404b in _IO_vfscanf () from /lib/tls/i686/cmov/libc.so.6
#1 0xb7e1dd2b in scanf () from /lib/tls/i686/cmov/libc.so.6         //所以是 scanf() 调用错误导致的，如第二个参数少 & 符号
#2 0x0804839f in main () at main.c:6
```

## 汇编相关

```
(gdb) disassemble                               //反汇编当前函数
(gdb) disassemble 0x0804839f                    //反汇编指定地址函数
(gdb) disassemble func                          //反汇编指定名称函数

(gdb) si                                        //stepi,一条指令一条指令的单步调试,step 是一行代码一行代码的单步调试

(gdb) info registers                            //显示所有寄存器的当前值
(gdb) i $esp                                    //gdb 中表示寄存器名时前面加 $
```
