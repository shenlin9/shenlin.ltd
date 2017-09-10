---
title: C 语言 - Linux C 编程一站式学习 - C 语言本质
categories: C 语言
tags: C 语言
---

<!--more-->

## 计算机中数的表示

二进制转十进制
1101
=1x2[0]+0x2[1]+1x2[2]+1x2[3]
=1x2[3]+1x2[2]+0x2[1]+1x2[0]
=1x2[1]x2[1]x2[1]+1x2[1]x2[1]+0x2[1]+1x2[0]
=(1x2[1]x2[1]+1x2[1]+0)x2[1]+1x2[0]
=((1x2[1]+1)x2[1]+0)x2[1]+1x2[0]
=((1x2[1]+1)x2[1]+0)x2[1]+1
=(((0x2[1]+1)x2[1]+1)x2[1]+0)x2[1]+1
=13

0.011
=0x2[-1]+1x2[-2]+1x2[-3]
=1x2[-3]+1x2[-2]+0x2[-1]
=1x2[-1]x2[-1]x2[-1]+1x2[-1]x2[-1]+0x2[-1]
=(1x2[-1]x2[-1]+1x2[-1]+0)x2[-1]
=((1x2[-1]+1)x2[-1]+0)x2[-1]
=(((0x2[-1]+1)x2[-1]+1)x2[-1]+0)x2[-1]
=0.375

十六进制转十进制
CA7D = (((((0x16+C)x16)+A)x16+7)x16)+D = 51837

八进制转十进制
237 = ((0x8+2)x8+3)x8+7 = 159

N 进制数 abcd 转为 10 进制
abcd = (((a * N) + b) * N + c) * N + d

10 进制数 abcd 转为 N 进制，除以 N 取余数
abcd = (abcd % N)

## 数据类型


## 运算符


## 计算机体系结构


## x86 汇编程序

### 目标文件

```c
$ readelf -a max.o

ELF Header:
    Magic: 7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00
    Class: ELF32
    Data: 2's complement, little endian
    Version: 1 (current)
    OS/ABI: UNIX - System V
    ABI Version: 0
    Type: REL (Relocatable file)
    Machine: Intel 80386
    Version: 0x1
    Entry point address: 0x0
    Start of program headers: 0 (bytes into file)
    Start of section headers: 200 (bytes into file)
    Flags: 0x0
    Size of this header: 52 (bytes)
    Size of program headers: 0 (bytes)
    283
    Number of program headers: 0
    Size of section headers: 40 (bytes)
    Number of section headers: 8
    Section header string table index: 5

Section Headers:
    [Nr] Name Type Addr Off Size ES Flg Lk Inf Al
    [ 0] NULL 00000000 000000 000000 00 0 0 0
    [ 1] .text PROGBITS 00000000 000034 00002a 00 AX 0 0 4
    [ 2] .rel.text REL 00000000 0002b0 000010 08 6 1 4
    [ 3] .data PROGBITS 00000000 000060 000038 00 WA 0 0 4
    [ 4] .bss NOBITS 00000000 000098 000000 00 WA 0 0 4
    [ 5] .shstrtab STRTAB 00000000 000098 000030 00 0 0 1
    [ 6] .symtab SYMTAB 00000000 000208 000080 10 7 7 4
    [ 7] .strtab STRTAB 00000000 000288 000028 00 0 0 1

Key to Flags:
    W (write), A (alloc), X (execute), M (merge), S (strings)
    I (info), L (link order), G (group), x (unknown)
    O (extra OS processing required) o (OS specific), p (processor specific)

There are no section groups in this file.

There are no program headers in this file.

Relocation section '.rel.text' at offset 0x2b0 contains 2 entries:
    Offset Info Type Sym.Value Sym. Name
    00000008 00000201 R_386_32 00000000 .data
    00000017 00000201 R_386_32 00000000 .data

There are no unwind sections in this file.

Symbol table '.symtab' contains 8 entries:
    Num: Value Size Type Bind Vis Ndx Name
    0: 00000000 0 NOTYPE LOCAL DEFAULT UND
    1: 00000000 0 SECTION LOCAL DEFAULT 1
    2: 00000000 0 SECTION LOCAL DEFAULT 3
    3: 00000000 0 SECTION LOCAL DEFAULT 4
    4: 00000000 0 NOTYPE LOCAL DEFAULT 3 data_items
    5: 0000000e 0 NOTYPE LOCAL DEFAULT 1 start_loop
    6: 00000023 0 NOTYPE LOCAL DEFAULT 1 loop_exit
    7: 00000000 0 NOTYPE GLOBAL DEFAULT 1 _start

No version information found in this file.
```

### 可执行文件

```asm
$ readelf -a max

ELF Header:
    Magic: 7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00
    Class: ELF32
    Data: 2's complement, little endian
    Version: 1 (current)
    OS/ABI: UNIX - System V
    ABI Version: 0
    Type: EXEC (Executable file)
    Machine: Intel 80386
    Version: 0x1
    Entry point address: 0x8048074
    Start of program headers: 52 (bytes into file)
    Start of section headers: 256 (bytes into file)
    Flags: 0x0
    Size of this header: 52 (bytes)
    Size of program headers: 32 (bytes)
    Number of program headers: 2
    Size of section headers: 40 (bytes)
    Number of section headers: 6
    Section header string table index: 3

Section Headers:
    [Nr] Name Type Addr Off Size ES Flg Lk Inf Al
    [ 0] NULL 00000000 000000 000000 00 0 0 0
    [ 1] .text PROGBITS 08048074 000074 00002a 00 AX 0 0 4
    [ 2] .data PROGBITS 080490a0 0000a0 000038 00 WA 0 0 4
    [ 3] .shstrtab STRTAB 00000000 0000d8 000027 00 0 0 1 288
    [ 4] .symtab SYMTAB 00000000 0001f0 0000a0 10 5 6 4
    [ 5] .strtab STRTAB 00000000 000290 000040 00 0 0 1

Key to Flags:
    W (write), A (alloc), X (execute), M (merge), S (strings)
    I (info), L (link order), G (group), x (unknown)
    O (extra OS processing required) o (OS specific), p (processor specific)

There are no section groups in this file.

Program Headers:
    Type Offset VirtAddr PhysAddr FileSiz MemSiz Flg Align
    LOAD 0x000000 0x08048000 0x08048000 0x0009e 0x0009e R E 0x1000
    LOAD 0x0000a0 0x080490a0 0x080490a0 0x00038 0x00038 RW 0x1000

Section to Segment mapping:
    Segment Sections...
    00 .text
    01 .data

There is no dynamic section in this file.

There are no relocations in this file.

There are no unwind sections in this file.

Symbol table '.symtab' contains 10 entries:
    Num: Value Size Type Bind Vis Ndx Name
    0: 00000000 0 NOTYPE LOCAL DEFAULT UND
    1: 08048074 0 SECTION LOCAL DEFAULT 1
    2: 080490a0 0 SECTION LOCAL DEFAULT 2
    3: 080490a0 0 NOTYPE LOCAL DEFAULT 2 data_items
    4: 08048082 0 NOTYPE LOCAL DEFAULT 1 start_loop
    5: 08048097 0 NOTYPE LOCAL DEFAULT 1 loop_exit
    6: 08048074 0 NOTYPE GLOBAL DEFAULT 1 _start
    7: 080490d8 0 NOTYPE GLOBAL DEFAULT ABS __bss_start
    8: 080490d8 0 NOTYPE GLOBAL DEFAULT ABS _edata
    9: 080490d8 0 NOTYPE GLOBAL DEFAULT ABS _end

No version information found in this file.
```


## 链接


## 预处理


## MakeFile


## 指针

```
int i = 10, j = 11;

int *p = &i;    // 初始化时 *p 表示定义指针变量 p，??? 这里的 * 号也是指针间接寻址运算符吗
p = &j;         // 这里 p 就表示指针变量本身
*p = 11;        // 这里 *p 表示指针变量指向的变量的值，* 号是指针间接寻址运算符
```

## 函数接口


## C 标准库


## 链表


## 二叉树


## 哈希表


