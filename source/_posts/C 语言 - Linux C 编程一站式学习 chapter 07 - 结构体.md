---
title: C 语言 - Linux C 编程一站式学习 chapter 07 - 结构体
categories: 
    - C 语言
    - Linux C 编程一站式学习
tags: C 语言
---

结构体

<!--more-->

## 基本类型和复合类型

Primitive Type 基本类型 

Compound Type 复合类型

标量类型包括算术类型和指针类型，可以表示零或非零，可以参与逻辑与、逻辑或、逻辑非运算…… C 语言没有布尔类型

算术类型包括整型和浮点型

## 复合类型定义

C99 定义了复合类型 Complex，需包括头文件
```c
#include <complex.h>
int main(void){
    complex a;
}
```

## 结构体

定义了结构体数据类型
```c
struct complex_struct {
    double x,y;
};
```
* 复合类型定义也是一种声明，因此也需要分号结尾
* complex_struct 称为 tag

声明结构体类型的变量
```c
struct complex_struct a,b;
```

上面的两条语句可整合为
```c
struct complex_struct {
    double x,y;
} a,b;
```
* 把从 "struct" 到 "}" 看做是个 "int" 就好理解了，`int a,b;` 就是正常的变量声明方式

结构体变量可以在定义时初始化
```c
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
```c
struct complex_struct { int x,y; } a;
a = {3, 4}; //这样不行
```

但可以使用下面这种赋值语法
```c
struct complex_struct { int x,y; } a;
a = (struct complex_struct){5,6};
```

还可以针对个别成员初始化
```c
struct complex_struct { int x,y; };
struct complex_struct a = { .y = 8 };
```
* 则 a.x = 0, a.y = 8，注意除 y 外的其他成员都被初始化为 0，而不是其他成员不作处理保留原值

定义结构体时也可以没有Tag，但这个结构体不能被再次引用
```c
struct {
    double x,y;
} a,b;
```

访问结构体成员使用后缀运算符点
```c
a.x = 10;
a.y = 12;
```

结构体可以嵌套
```c
struct segment {
    struct complex_struct start;
    struct complex_struct end;
}
```

嵌套结构体初始化
```c
//嵌套初始化
struct segment s = {{1.0, 2.0}, {4.0, 6.0}};

//平坦初始化
struct segment s = {1.0, 2.0, 4.0, 6.0};

//成员逐一初始化
struct segment s = {.start.x = 3.0, .end.y = 4.0};
```

访问嵌套结构体成员
```c
s.start.x = 1.2;
s.end.y = 3.2;
```

结构体不能进行算术运算，逻辑运算
但可以用一个结构体初始化或赋值给另一个结构体
```c
struct complex_struct { int x,y; };
struct complex_struct a = { 3, 4 };
struct complex_struct b = a;
struct complex_struct c;
c = a;
```

结构体可以作为函数参数
```c
int main(void){
    struct car {int wheeels = 4; char color = 'red';} mycar;
    test(struct mycar);
}

void test(struct thiscar){
    printf("%d",thiscar.wheels);
}
```

