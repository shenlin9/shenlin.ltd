---
title: C 语言 - Linux C 编程一站式学习
categories: C语言
tags: C语言

---

## 学习一门编程语言

要注意下面三个方面：

* 提供了哪些儿 Primitive，如基本类型、基本运算符、表达式和语句
* 提供了哪些儿组合规则，如基本类型如何组合成复合类型，基本的表达式和语句如何组合成复合的表达式和语句
* 提供了哪些儿抽象机制，包括数据抽象和过程抽象

## C 标准

old style C, 89, 99, 11

## C 标准库

C 标准由两部分组成：C 的语法和 C 标准库，实现 C 标准，就要实现 C 编译器和 C 标准库

C 标准库定义了一组标准头文件，每个头文件包含一些儿函数、变量、类型声明和宏定义。

Linux 平台使用最广泛的 C 函数库是 glibc，它提供了一组头文件和库文件，最基本最常用的标准库函数和系统函数都在库文件 libc.so

使用数学函数需要包含头文件 math.h，它位于 libm.so 库文件中，编译时需要加 `-lm` 参数来告诉编译器去这个库文件中找

但大部分常用的函数位于 libc.so 库文件中，但编译时无需额外参数，是因为 gcc 默认已经添加了 `-lc` 选项

## main 函数

main 函数是被操作系统调用的，因此返回值也是给操作系统的。

$? 表示上一条命令的退出状态
```
$ ./a.out
$ echo $?
```

### main 函数的写法

系统在调用 main 函数时是传递参数的，它的标准形式：
```
int main(int argc, char *argv[])
```

不使用系统传递的参数也可以这种写法
```
int main(void)
```

最老的 old style C 的写法
```
main(){}
```
* 默认返回 int 类型
* 参数类型和个数不确定
* 但这种宽松的规定使得编译器无法检查程序中可能的 bug
* 为了向后的兼容还可以这样写，但会被警告，不推荐的写法

除了上面的形式，其他写法都是错误或不可移植的。

## void

任何表达式都有值和类型两个基本属性

函数 void test(void) 没有返回值，那么表达式 test() 的值是什么呢？

没有返回值的函数的返回类型是 void，则表示它有一个 void 类型的值，这样设计则任何表达式都有值，编译器做语法解析时不用考虑特殊情况

从语义上规定 void 类型的返回值不能参与运算，因此 这种写法不能通过语义检查：test() + 1

这种设计兼顾了语法上的一致和语义上没有矛盾，是 C 的实现方法，其他语言可能使用其他方法，如分为有返回值的函数，和没有返回值的过程 procedure

## 声明和定义

### 变量

只有分配了内存空间的变量声明才可以称为变量定义

声明
```
short age;
```

定义
```
short age = 12;
```

### 函数

函数原型
```
void threeline(void)
```
* 编译器要根据函数原型获取函数的参数类型、参数个数、返回值等信息，然后在调用函数处才知道怎么生成指令

函数声明
```
void threeline(void);

//声明时可以不指定参数名
void threeline(int,char);

//可以在函数内部声明
int main(void){

    void test(void);

    test();
}

void test(void){
    printf("hahaah");
}
```
* 函数原型后面加分号
* 第二种方式：函数声明时可以只指定参数类型，不指定参数名
* 第三种方式：函数声明也可以在函数内部，则其作用范围也仅为函数内部
* 可以在函数内部声明函数，但不能在函数内部定义函数，即 C 标准不支持嵌套函数定义，但 gcc 的扩展特性允许嵌套定义

函数定义
```
void threeline(void){}
```
* 函数原型加上函数体
* 编译器见到函数定义才生成指令

## 隐式声明

函数没有显式声明时则编译器默认隐式声明了函数，且默认返回值为 int 类型

若函数的返回值类型不是预期的 int，则警告

## 函数参数

old style C 的函数参数举例:
```
void foo(x,y,z)
int x;
char z;
{

}
```
* 参数之间使用逗号做分隔符即是从这里继承下来的

## 最少例外原则

rule of least surprise

一个语言的语法规则设计里不应该到处是例外，而 C++ 就充满了例外，导致没人能把 C++ 的所有规则牢记于心

## 形参实参

形参相当于函数中定义的变量，调用函数传递参数的过程相当于定义形参的变量并且用实参的值来初始化

## 局部变量、全局变量

### 分配释放空间

局部变量在每次函数调用时分配存储空间，在函数返回时释放存储空间

全局变量在程序开始运行时分配存储空间，在程序结束时释放存储空间

### 调用范围

局部变量只能在函数内部调用，全局变量可任意位置调用

全局变量使用方便，在各函数内部都可以访问，但也因此导致全局变量的读写顺序不好理清，易导致问题，因此要慎用，可以通过函数传参代替就不要使用全局变量

### 初始化

全局变量定义时不初始化其值为 0，但局部变量不初始化则其值是不确定的，所以局部变量在使用前必须先初始化。

局部变量可以使用任意合法表达式初始化

全局变量初始化只能使用常量表达式，因为全局变量在程序开始运行时就要被初始化，所以保存在可执行文件里的初始化值在编译时就要计算出来，而非常量表达式需要运行时才能计算出来，所以为了简化编译器的实现，C 语言规定了全局变量只能使用常量表达式初始化。

### 语句块局部变量

语句块的局部变量通常是为了定义比函数的局部变量更局部的变量

```
#include <stdio.h>
int main(void){

    int i = 1;
    {
        int i = 2;    
        int j = 2;
        printf("%d\n",i); //输出 2
    }
    //printf("%d\n",j);  这里不注释掉即出错，提示未声明变量 j
}
```

## 取模和整除

% 取模运算符，即两个数相除的余数，结果的符号总是和被除数相同
```
#include <stdio.h>
int main(void){
    printf("%d\n",7%-2);  //输出 1
    printf("%d\n",-7%2);  //输出 -1
}
```

整数除法运算总是趋向 0
```
printf("%d\n",7/2); //3
printf("%d\n",-7/2); //-3
```

## 逻辑运算

关于逻辑运算的数学体系称为布尔代数，以创始人布尔(Boolean Algebra)命名

## return

函数的返回值相当于定义了一个临时变量，临时变量的数据类型和函数的返回类型相同，并且用 return 后面的语句初始化这个临时变量
```
int i = 20;

if is_even(i)
    printf("%d是偶数\n",i);

int is_even(x){
    return !(x % 2)
}

//执行过程相当于

int i = 20;

x = i;                      //函数内的局部变量 x

int temp_var = !(x % 2)     //函数退出，局部变量 x 被释放，需要创建临时变量 temp_var 来保存函数的返回值

if (temp_var)               //临时变量 temp_var 使用完就释放
    printf("%d是偶数\n",i);
```
* 虽然函数的返回值相当于临时变量，但不能向其赋值，如 is_even(20) = 1;

返回时不用加括号
```
return 1;

return(1);
```
* 表达式里加括号是为了改变优先级，但这里不起任何作用，所以第一种写法即可

## Dead Code

代码路径 Code Path 里永远执行不到的代码，有 Dead Code 就表示肯定有 Bug，因为不会故意写一行永远不会执行的代码，所以肯定是程序的运行和你的预想不一样

## Hard Coding

硬编码，直接写死在程序里，而不是通过常量、变量来保存

## 递归定义和Base Case

递归定义需要基础条件 Base Case

如阶乘的定义为: n!=n * (n-1)!，即3的阶乘等于3乘以2的阶乘，2的阶乘又等于2乘以1的阶乘，一直到0的阶乘，然后定义0的阶乘等于1

n! = n * (n-1)!
0! = 1

递归和循环是等价的，一些儿语言没有循环只有递归

## Terms

Leap Of Faith 信仰之跃，先假设某个未完成的函数返回结果是正确的，在此基础上完成其他部分。

infinite recursion  无穷递归

infinite loop  无限循环

POSIX time 或者 Epoch time
    Unix 系统诞生于 1969 年，因此所有类 Unix 系统都把 1970-01-01 0:00:00 称为 Epoch time 或 Unix 时间戳

## 循环

下面的例子中 n 忽大忽小，但最后都是 1，不会死循环，这就是著名的 3x+1 问题，目前还未被证明
```
n = 7;
while (n != 1) {

    if (n % 2 = 0)
        n = n / 2;
    else
        n = n * 3 + 1;
}
```
3x+1 猜想：任取一个自然数，如果它是偶数，我们就把它除以2，如果它是奇数，我们就把它乘3再加上1。使用新得到的新自然数反复变换，最后结果为1。

## 标号

如 switch 语句中的 case,default，goto语句的
```
switch () {
    case 1:
        printf("1");
        break;
    case 2:
        printf("2");
        break;
    default:
        printf("others");
}

void test(void){

    int i = 0;
    goto mark;
    printf("%d",i);

mark:
    i = 1;
}
```
* goto 不能跨函数

## Duff's Device

达夫设备，一种特殊的代码写法，利用 switch 的语句块和循环语句的语句块没有本质区别，实现了巧妙的代码优化

```
#include <stdio.h>
int main(void){
    int i = 0,j = 0;
    switch (i) {
        case 0: do {
        case 1:     ++j;
        case 2:     ++j;
        case 3:     ++j;
        case 4:     ++j;
        case 5:     ++j;
        case 6:     printf("%d,",j);
        } while (j < 3);
    }
    printf("\nj = %d\n",j);
}

//输出
1,2,3,4,5,
j = 5
```
http://blog.csdn.net/kingmax26/article/details/5252657

## 基本类型和复合类型

Primitive Type 基本类型 Compound Type 复合类型

标量类型包括算术类型和指针类型，可以表示零或非零，可以参与逻辑与、逻辑或、逻辑非运算…… C 语言没有布尔类型

算术类型包括整型和浮点型

## 复合类型定义

C99 定义了复合类型 Complex，需包括头文件
```
#include <complex.h>
int main(void){
    complex a;
}
```

## 结构体

定义了结构体数据类型
```
struct complex_struct {
    double x,y;
};
```
* 复合类型定义也是一种声明，因此也需要分号结尾
* complex_struct 称为 tag

声明结构体类型的变量
```
struct complex_struct a,b;
```

上面的两条语句可整合为
```
struct complex_struct {
    double x,y;
} a,b;
```
* 把从 "struct" 到 "}" 看做是个 "int" 就好理解了，`int a,b;` 就是正常的变量声明方式

结构体变量可以在定义时初始化
```
struct complex_struct { int x,y; };
struct complex_struct a = { 3, 4 }; //初始化的数据成员按顺序给结构体成员赋值
int main(void){
    printf("%d,%d",a.x,a.y);
}
```
* 结构体可以定义在全局作用域中
* 结构体变量在全局初始化时也是只能使用常量表达式
* 数据成员比结构体成员多将报错
* 数据成员比结构体成员末尾多一个逗号不报错
* 数据成员比结构体成员少，则后面的结构体成员初始化为0

`{}`可以初始化结构体变量，但不能用于结构体变量的赋值
```
struct complex_struct { int x,y; } a;
a = {3, 4}; //这样不行
```

但可以使用下面这种赋值语法
```
struct complex_struct { int x,y; } a;
a = (struct complex_struct){5,6};
```

还可以针对个别成员初始化
```
struct complex_struct { int x,y; };
struct complex_struct a = { .y = 8 };
```
* 则 a.x = 0, a.y = 8，注意除 y 外的其他成员都被初始化为 0，而不是其他成员不作处理保留原值

定义结构体时也可以没有Tag，但这个结构体不能被再次引用
```
struct {
    double x,y;
} a,b;
```

访问结构体成员使用后缀运算符点
```
a.x = 10;
a.y = 12;
```

结构体可以嵌套
```
struct segment {
    struct complex_struct start;
    struct complex_struct end;
}
```

嵌套结构体初始化
```
//嵌套初始化
struct segment s = {{1.0, 2.0}, {4.0, 6.0}};

//平坦初始化
struct segment s = {1.0, 2.0, 4.0, 6.0};

//成员逐一初始化
struct segment s = {.start.x = 3.0, .end.y = 4.0};
```

访问嵌套结构体成员
```
s.start.x = 1.2;
s.end.y = 3.2;
```

结构体不能进行算术运算，逻辑运算
但可以用一个结构体初始化或赋值给另一个结构体
```
struct complex_struct { int x,y; };
struct complex_struct a = { 3, 4 };
struct complex_struct b = a;
struct complex_struct c;
c = a;
```

结构体可以作为函数参数
```
int main(void){
    struct car {int wheeels = 4; char color = 'red';} mycar;
    test(struct mycar);
}

void test(struct thiscar){
    printf("%d",thiscar.wheels);
}
```

## 后缀运算符

i++ 后缀++
i-- 后缀--
struct.item 结构体访问成员
array[1] 数组下标
func() 函数调用

后缀运算符优先级最高，其次是单目运算符，如正负号、逻辑非!、前缀++、前缀--

## 数组

VLA : Variable Length Array 可变长度数组，C99 允许声明数组时长度使用变量

数组定义：像结构体一样初始化，未赋值的元素初始化为 0
```
int arr[3] = {2, 4, };  //arr[2] = 0;
```

数组定义：单个元素赋值
```
int arr[3] = {[2] = 3};
```

数组定义：不指定长度
```
int arr[] = {2, 4, }; //数组只有这两个元素
```

下标越界问题
```
int arr[] = {'a','b','c'};
printf("%c",arr[4]);
```
* 上面的代码下标越界但并没有错误
* C 语言在编译和执行时都不检查下标越界
* 编译时不检查是因为数组下标是否越界有时运行时才知道，如使用指针代替数组名访问数组元素时，只有运行时才知道是否越界
* 运行时每次访问数组元素都检查是否越界对性能影响较大

**数组类型有一条特殊的规则：数组类型做右值使用时，自动转换成指向数组首元素的指针**

因为上面的特殊规则，所以数组和结构体不同，数组不能互相赋值和初始化
```
int a[3] = {1,2,3};
int b[3];
b = a;
printf("%d",b[2]); // error: assignment to expression with array type
```
* a 作为数组在作为右值时自动转换为了指针，而作为左值的 b 仍然是数组，类型不同

既然数组无法相互赋值，则自然也不能作为函数的参数或函数的返回值，因为它们都是通过局部变量赋值和临时变量赋值实现的

## 多维数组

多维数组也可扁平初始化
```
int a[3][2] = {1, 2, 3, 4, 5, 6};
```
* 经测试好像不行

也可以嵌套初始化
```
int a[][2] = {
 {1, 2},
 {3, 4},
 {5, 6},
};
```
* 多维数组初始化时只有第一维的长度可以省略

或者 C99 的 Memberwise 初始化
```
int a[3][2] = {[0][1]=2, [2][0]=5};
```

上面的二维数组逻辑上为 3 行 2 列的表格，但物理存储上和一维数组一样是连续存储的，相当于把每行都拼接在一起成了一个更长的行，
C 语言采用的这种存储方式成为 Row-major，有的语言是把列拼接起来，称为 Column-major

## 字符串

以 NULL 字符结尾的一串字符就是字符串，NULL 字符就是 ASCII 码为 0 的字符，为不可见字符

分为字符串字面值和字符数组，字符串字面值即直接写在程序里的字符串，如"abc"

### 字符串字面值

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

### 字符数组

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

//数组长度刚刚好字符串本身，但没有给 \0 字符留空间时也不报错 ??? 这个确实不报错，但系统是否自动添加了NULL字符还需确认
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

### 多维字符数组

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

## 数据驱动的编程

data-driven programming

写代码时相对于控制流程和算法，选择正确的数据结构是更重要的

举例：根据输入的数字 1~7 输出对应的星期几
```
void print_day(short);

int main(void){
    int i = 0;
    while(1){
        printf("please input number : ");
        scanf("%d",&i);
        if (i > 0 && i < 8)
            print_day(i);
        else
            printf("err:invalid number\n");
    }
}

//良好的数据结构选择，没有任何的if else 判断
void print_day(short i){
    char dayName[8][10] = {"", "monday", "tuesday", "wednesday", "thursday", "friday", "saturday", "sunday"};
    printf("%s\n",dayName[i]);
}
```

## 随机数

计算机执行的每一条指令的结果都是确定的，没有一条指令的结果是随机的，
因此调用C函数库得到的随机数其实是伪随机数 pseudorandom，是使用数学公式计算出来的确定的数，只是看起来像随机，
随机数的种子即是数学公式里的参数，种子确定的话，其生成的整个随机数序列也是确定和循环的。
从统计上看生成的随机数是接近均匀分布的

C 语言生成随机数需要包含头文件 stdlib.h，使用 rand 函数，没有参数，返回 0 到 RAND_MAX 之间接近均匀分布的整数。
RAND_MAX 是 stdlib.h 中定义的常量，是一个非常大的整数，不同的平台其取值不同
通常使用时通过取模运算(如 rand() % 10)限定生成的值的范围，而不是直接使用默认的 0~RAND_MAX 之间的范围。

srand 可以设定随机数的种子
rand 然后生成随机数

## 常量

定义常量
```
#define N 20;
```

## 预处理

preprocess

编译器的工作分为预处理和编译两个阶段

以 # 号开头的行表示 预处理指示 Preprocessing Directive

查看文件在预处理之后编译之前的内容，两条命令效果相同
```
$ gcc -E test.c

$ cpp test.c
```
* cpp 表示 C preprocess

可以看到预处理将会：
* 展开包含的头文件的代码
* 把定义的常量替换为定义的真正数值

## 编码风格

代码是写给人看的，顺便给机器去执行。

### 空白

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

### 缩进

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

### 其他

带语句块的 switch, for, while, if, else 的 `{}` 要写在关键字同一行
但是函数的'{}'要独占一行
```
if (i > 1) {

} else if {

}

int getName(void)
{

}
```

switch 语句的 case, default 不缩进，标号下面的语句缩进
```
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
```
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
```
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
```
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

### 注释

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
```
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

### 标识符命名

### 函数

### 缩进工具
