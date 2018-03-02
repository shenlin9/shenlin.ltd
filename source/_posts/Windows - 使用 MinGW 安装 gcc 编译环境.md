---
title: Windows 下使用 MinGW 安装 gcc 编译环境
categories:
  - Windows
tags:
  - Windows
  - MinGW
  - gcc
  - g++
---

什么是gcc / g++

<!--more-->

首先说明：小写的 gcc 和大写的 GCC 是两个不同的东西

GCC:GNU Compiler Collection(GNU 编译器集合)，它可以编译C、C++、JAV、Fortran、Pascal、Object-C、Ada等语言。

gcc 是 GCC中的GNU C Compiler（C 编译器）

g++ 是 GCC中的GNU C++ Compiler（C++编译器）

<!--more-->

一个有趣的事实就是，就本质而言，gcc和g++并不是编译器，也不是编译器的集合，它们只是一种驱动器，根据参数中要编译的文件的类型，调用对应的GUN编译器而已

由于编译器是可以更换的，所以gcc不仅仅可以编译C文件

所以，更准确的说法是：gcc调用了C compiler，而g++调用了C++ compiler

gcc 和 g++ 的主要区别

1. 对于 *.c和*.cpp文件，gcc分别当做c和cpp文件编译（c和cpp的语法强度是不一样的）

2. 对于 *.c和*.cpp文件，g++则统一当做cpp文件编译

3. 使用g++编译文件时，g++会自动链接标准库STL，而gcc不会自动链接STL

4. gcc在编译C文件时，可使用的预定义宏是比较少的

5. gcc在编译cpp文件时/g++在编译c文件和cpp文件时（这时候gcc和g++调用的都是cpp文件的编译器），会加入一些额外的宏

## MinGW

MinGW - Minimalist GNU for Windows

## 安装

下载 mingw-get 安装器

    https://sourceforge.net/projects/mingw/files/

    找到这行点击下载
    Looking for the latest version? Download mingw-get-setup.exe (86.5 kB) 

执行下载的文件 mingw-get-setup.exe 安装 mingw-get，并把安装目录写入 Path 路径

再用 mingw-get 安装 gcc 和 g++

```
//命令帮助
λ mingw-get -h


λ mingw-get list|grep gcc
Package: mingw32-gcc                                  Subsystem: mingw32
Package: mingw32-gcc-ada                              Subsystem: mingw32
Package: mingw32-gcc-core-deps                        Subsystem: mingw32
Package: mingw32-gcc-fortran                          Subsystem: mingw32
Package: mingw32-gcc-g++                              Subsystem: mingw32
Package: mingw32-gcc-objc                             Subsystem: mingw32

//安装
λ mingw-get install gcc g++

//复制一份改名为 make 方便操作
λ cp mingw32-make.exe make.exe

//验证安装
λ gcc -v

λ mingw32-make -version

λ make -v
```

新建环境变量：LIBRARY_PATH 
    C:\MinGW\lib

新建环境变量：C_INCLUDEDE_PATH
    C:\MinGW\include


完成，编译软件包时，若缺少 dll， 可能是有软件包未安装，如编译 SpaceVim 缺少 libmingwex，安装即可



