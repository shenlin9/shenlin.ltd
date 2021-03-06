---
title: C 语言 - Linux C 编程一站式学习 chapter 08 - 数组
categories: 
    - C 语言
    - Linux C 编程一站式学习
tags: C 语言
---

数组

<!--more-->

VLA : Variable Length Array 可变长度数组，C99 允许声明数组时长度使用变量

数组定义：像结构体一样初始化，未赋值的元素初始化为 0
```
int arr[3] = {2, 4, };  //arr[2] = 0;
```

数组定义：单个元素赋值
```c
int arr[3] = {[2] = 3};
```

数组定义：不指定长度
```c
int arr[] = {2, 4, }; //数组只有这两个元素
```

下标越界问题
```c
int arr[] = {'a','b','c'};
printf("%c",arr[4]);
```
* 上面的代码下标越界但并没有错误
* C 语言在编译和执行时都不检查下标越界
* 编译时不检查是因为数组下标是否越界有时运行时才知道，如使用指针代替数组名访问数组元素时，只有运行时才知道是否越界
* 运行时每次访问数组元素都检查是否越界对性能影响较大

**数组类型有一条特殊的规则：数组类型做右值使用时，自动转换成指向数组首元素的指针**

因为上面的特殊规则，所以数组和结构体不同，数组不能互相赋值和初始化
```c
int a[3] = {1,2,3};
int b[3];
b = a;
printf("%d",b[2]); // error: assignment to expression with array type
```
* a 作为数组在作为右值时自动转换为了指针，而作为左值的 b 仍然是数组，类型不同

既然数组无法相互赋值，则自然也不能作为函数的参数或函数的返回值，因为它们都是通过局部变量赋值和临时变量赋值实现的

## 多维数组

多维数组也可扁平初始化
```c
int a[3][2] = {1, 2, 3, 4, 5, 6};
```
* 经测试好像不行

也可以嵌套初始化
```c
int a[][2] = {
 {1, 2},
 {3, 4},
 {5, 6},
};
```
* 多维数组初始化时只有第一维的长度可以省略

或者 C99 的 Memberwise 初始化
```c
int a[3][2] = {[0][1]=2, [2][0]=5};
```

上面的二维数组逻辑上为 3 行 2 列的表格，但物理存储上和一维数组一样是连续存储的，相当于把每行都拼接在一起成了一个更长的行，
C 语言采用的这种存储方式成为 Row-major，有的语言是把列拼接起来，称为 Column-major

## 字符串

以 NULL 字符结尾的一串字符就是字符串，NULL 字符就是 ASCII 码为 0 的字符，为不可见字符

分为字符串字面值和字符数组，字符串字面值即直接写在程序里的字符串，如"abc"

## 字符串字面值

字符串字面值是数组类型，每个元素是字符型
```
"hello,world.\n";
```
* 每个字符串都是以 NULL 结尾的字符串，即末尾有个 \0 字符，就是 ASCII 码为 0 的 NULL 字符，为不可见字符
* 所以上面的字符串占用字节是 14 个
    * "hello,world." 每个字符占用一个字节共占用 12 个
    * "\n" 换行符占用 1 个
    * "\0" NULL 字符占用 1 个

因为字符串字面值是数组类型，所以可以通过下标的方式访问
```
char c = "hello,world.\n"[1];  //输出 e
```

字符串字面值是只读到，所以不能通过下标的方式更改字符串字面值
```
"hello,world.\n"[1] = "E";
```

字符串字面值和数组一样，做右值使用时，自动转换为指向数组首元素的指针
```
printf("hello,world.\n");
```
* printf 的第一个参数就是指针类型的，所以上面的字符串其实就是传送的指针给它

## 字符数组

字符数组可以通过字符串字面值来做初始化
```
char str[10] = "hello";

//相当于

char str[10] = {'h', 'e', 'l', 'l', 'o', \0, \0, \0, \0, \0"};
```
* 没有指定初始值的元素自动初始化为 NULL 字符

使用字母值初始化字符数组时最好不指定数组长度，让编译器自己计算
```
//数组长度长了浪费空间，短了编译错误
char str[4] = "hello"; //initializer-string for array of chars is too long

//数组长度刚刚好字符串本身，但没有给 \0 字符留空间时也不报错 ??? 这个确实不报错，但系统是否自动添加了 NULL 字符还需确认
char str[5] = "hello";

//最好这种方式
char str[] = "hello";
```

使用`%s`占位符
```
printf("i will say : %s",str);
```
* 会从字符串 str 的开头一直打印到 NULL 字符为止
* 所以上面的刚好没有给 NULL 字符留空间的字符串可能会访问时越界

## 多维字符数组

```
char dayName[8][10] = {"", "monday", "tuesday", "wednesday", "thursday", "friday", "saturday", "sunday"};

//相当于

char dayName[8][10] = {
    {''},
    {'m', 'o', 'n', 'd', 'a', 'y'},
    ....
    ....
    {'s', 'u', 'n', 'd', 'a', 'y'},
} 
```
* 第二维为 10 是因为最长字符串"wednesday"为 9 位，再多留 1 位给 NULL 字符

