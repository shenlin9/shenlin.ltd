---
title: C 语言 - Linux C 编程一站式学习 chapter 09 - 编码风格
categories: 
    - C 语言
    - Linux C 编程一站式学习
tags: C 语言
---

代码是写给人看的，顺便给机器去执行。

<!--more-->

## 空白

双目运算符两侧加空格，单目运算符和操作数之间不加空格
```
i = i + 1;
i < 1
!(i < 1)
i++
-x
&a
```

后缀运算符和操作数之间不加空格
```
func(arg)
arr[2]
struct1.item1
```

关键字 if, while, for 和后面的左括号之间加空格，括号内的表达式要紧贴空格
```
if (i > 1)
for (i = 1; i < 10; i++)
while (i > 1)
```

逗号和分号之后要加空格，是英文的写作习惯
```
for (int i = 0; i < 10; i++)
func(arg1, ar2)
```

为了突出优先级也可以紧凑写
```
for (int i=0; i<10; i++)
```

但不能误导运算符优先级
```
a||b && c
```

## 缩进

unix 系统标准终端是 24 行 80 列，接近或超过 80 字符要折行，且折行后要使用空格对齐上面的参数会表达式
```
if (a > b
    && c > d
    && e > f)

foo(sqrt(x*x + y*y),
    a[i-1] + b[i-1] + c[i-1])
```

较长字符串写成多行
```
printf("this is a very long sentence,"
       "and better make a new line."
);
```
* C 编译器会自动把相邻的字符串连接在一起

变量定义时使用 tab 对齐
```
int j;
char str;
double l;
float f;

//对比

int     j;
char    str;
double  l;
float   f;
```

## 其他

带语句块的 switch, for, while, if, else 的 `{}` 要写在关键字同一行
但是函数的'{}'要独占一行
```c
if (i > 1) {

} else if {

}

int getName(void)
{

}
```

switch 语句的 case, default 不缩进，标号下面的语句缩进
```c
switch (i) {
case 1:
    printf("one");
    break;
case 2:
    printf("two");
    break;
case 3:
    printf("three");
    break;
default:
    printf("others");
}
```

goto 语句的标号始终写在行首，不论标号下的缩进多少
```c
function test()
{
    if (i > 1) {
        goto Err; 
    }

    if (i < 1) {
        if (j > 1) {
Err:
            printf("err");
        }
    }
}
```

代码中每个逻辑段使用一个空行隔开，如头文件、全局变量定义、函数定义之间
```c
#include <stdio.h>
#include <stdlib.h>

int wheels  = 4;
int doors   = 2;

int main(void)
{

}

int test(void)
{

}
```

函数内部，根据相关性用空行分隔，如变量定义之后、return 语句之前加空行
```c
int test()
{
    int wheels  = 4;
    int doors   = 2;

    some logic here
    some logic here
    some logic here

    another logic here
    another logic here
    another logic here

    return 1;
}
```

## 注释

单行注释
```
/* 左右分别有两个空格把注释内容和界定符隔开 */
```

多行注释
```
/* 
 * 第一行和最后一行不写注释内容
 * 每一行注释内容前都有空格和注释符隔开
 * 以米字对齐
 */
```

注释使用场合
```c
/* 
 * 整个源文件的顶部注释
 * 顶头写不缩进
 * 包括了模块信息
 * 作者、版本历史
 * 等
 */

/**
 * 函数注释
 * 顶头写不缩进
 * 和函数定义之间不留空行
 * 说明函数的功能、参数、返回值、错误码等
 */
int rollWheels(void)
{

    /* 语句组注释：写在语句组上侧，和语句组之间不留空行，缩进和语句组相同 */
    some code here
    some code here
    some code here
    some code here

    some code here
    some code here  /* 代码行右侧简短注释，一般为单行，对当前代码行做说明，和代码之间空格隔开，最好所有右侧注释可以对齐 */
    some code here
}


/*
 * 复杂的结构体定义特别需要注释
 * 根据说明需要，可以包括结构体本身的注释
 * 还可以包括语句组注释，行右侧注释
 */
struct runqueue{

    /*
    * This is part of a global counter where only the total sum
    * over all CPUs matters. A task can increase this counter on
    * one CPU and if it got migrated afterwards it may decrease
    * it on another CPU. Always updated under the runqueue lock:
    */
    unsigned long nr_uninterruptible;

#ifdef CONFIG_SMP
    struct sched_domain *sd;

    /* For active balancing */
    int active_balance;
    int push_cpu;

    task_t *migration_thread;
    struct list_head migration_queue;
    int cpu;
#endif
};

/*
 * 复杂的宏定义和变量声明也需要注释，举例：
 */

/* TICK_USEC_TO_NSEC is the time between ticks in nsec assuming real ACTHZ and */
/* a value TUSEC for TICK_USEC (can be set bij adjtimex) */
#define TICK_USEC_TO_NSEC(TUSEC) (SH_DIV (TUSEC * USER_HZ * 1000,
ACTHZ, 8))

/* some arch's have a small-data section that can be accessed register-relative
 * but that can only take up to, say, 4-byte variables. jiffies being part of
 * an 8-byte variable may not be correctly accessed unless we force the issue
*/
#define __jiffy_data __attribute__((section(".data")))

/*
 * The 64-bit value is not volatile - you MUST NOT read it
 * without sampling the sequence number in xtime_lock.
 * get_jiffies_64() will do this for you as appropriate.
 */
extern u64 __jiffy_data jiffies_64;
extern unsigned long volatile __jiffy_data jiffies;
```

关于函数注释：
* 函数内的语句组注释和行右侧注释要尽可能少用
* 原因是注释是为了说明注释的代码能做什么，而不是为了说明是怎么做的
* 代码写的清晰，怎么做一目了然，如果需要注释才能解释清楚，说明代码可读性差
* 因此特别需要提醒注意的地方才应该用函数内注释

## 标识符命名

标识符要清晰明了，可以使用完整单词和易于理解的缩写，一些儿缩写惯例：
* count cnt
* block blk
* length len
* window win
* message msg
* number nr
* temporary temp tmp
* internationalization i18n
* trans x，如 transmit 缩写为 xmt

变量、函数、类型都采用全小写加下划线，如 函数名 radix_tree_insert， 类型名 struct radix_tree_root

常量、宏定义、枚举常量都采用全大写加下划线，如 RADIX_TREE_MAP_SHIFT

全局变量和全局函数的命名要详细，因为在整个项目的很多源文件都要用到，可以多几个单词和下划线，如 radix_tree_insert

不推荐使用大小写混合的驼峰命名法 CamelCase （以上都是 linux 内核的编码规范，所以不推荐大小写混用，多用下划线分割）

匈牙利命名法：

    Hungarian Notation

    微软发明的一种变量命名法，在变量名中用前缀表示类型，如 iCnt 中 i 表示 int, pMsg 中 p 表示 pointer

    **不**推荐使用匈牙利命名法，原因在于对于编译器毫无用处，因为编译器很清楚每个变量的类型，对于程序员加前缀说明标识符命名不够清楚

对于中国程序员，禁止使用汉语拼音做标识符，因为可读性极差

## 函数设计原则

* 一个函数应该只为完成一个功能，做好一件事情，利于重用和维护。
* 函数内部的缩进层次应少于 4 层，缩进层次太多说明设计过于复杂，应考虑分割成更小的函数来调用。
* 一个函数的行数不要太长，24 行的标准终端不要超过两屏，太长则应考虑分割函数，不过对于一个概念上简单，单纯只是长度较长的函数则没关系，如一个 switch 有非常多 case
* 执行函数就是执行一个动作，所以函数名通常应该包括动词，如 get_current, radix_tree_insert
* 比较重要的函数上方必须加注释，说明功能、参数、返回值、错误码等
* 一个函数内部的局部变量应限制在 5 到 10 个，再多说明函数复杂度太大，应考虑分割函数

## 缩进工具

linux 自带的 indent 工具，直接修改原文件为指定的编码风格，当然各逻辑段间隔的空行还需要手动添加
```bash
$ indent -kr -i4 -nut main.c
```
* -kr 选项表示采用 K&R 风格
* -i4 选项表示缩进 4 个空格
* -nut 选项表示不使用 tab 替换 4 个空格

