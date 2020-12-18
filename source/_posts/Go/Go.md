---
title: Go - Go
categories:
  - Go
tags:
  - Go
---

Go

<!--more-->

Go

## Go 环境变量

```
C:\Users\T460P>go env
set GO111MODULE=
set GOARCH=amd64
set GOBIN=
set GOCACHE=C:\Users\T460P\AppData\Local\go-build
set GOENV=C:\Users\T460P\AppData\Roaming\go\env
set GOEXE=.exe
set GOFLAGS=
set GOHOSTARCH=amd64
set GOHOSTOS=windows
set GONOPROXY=
set GONOSUMDB=
set GOOS=windows
set GOPATH=C:\Users\T460P\go
set GOPRIVATE=
set GOPROXY=https://proxy.golang.org,direct
set GOROOT=c:\go
set GOSUMDB=sum.golang.org
set GOTMPDIR=
set GOTOOLDIR=c:\go\pkg\tool\windows_amd64
set GCCGO=gccgo
set AR=ar
set CC=gcc
set CXX=g++
set CGO_ENABLED=1
set GOMOD=
set CGO_CFLAGS=-g -O2
set CGO_CPPFLAGS=
set CGO_CXXFLAGS=-g -O2
set CGO_FFLAGS=-g -O2
set CGO_LDFLAGS=-g -O2
set PKG_CONFIG=pkg-config
set GOGCCFLAGS=-m64 -mthreads -fno-caret-diagnostics -Qunused-arguments -fmessage-length=0 -fdebug-prefix-map=Z:\TEMP\go-build614126502=/tmp/go-build -gno-record-gcc-switches
```

其他环境变量都设置好了???，仅 GOPATH 环境变量需要自己设置，且名称要大写???，多个
目录用分号分隔：

Windows 下：通过系统环境变量

Linux 下设置 GOPATH：

## Go 目录

Go 强制规定的目录结构，在 GOPATH 下必须为下列三个目录：
* src   存放代码源文件
* pkg   存放编译后生成的包文件
* bin   存放编译后生成的的可执行文件

## Go 命令

常用 go 子命令
* go get        获取远程包，需已安装 git 或 hg

* go run        编译并运行程序
* go build      编译包和依赖
* go install    编译包和程序

* go test       运行测试文件，即以 `_test.go` 结尾的文件
* go fmt        格式化源代码，一般让 IDE 保存时自动调用
* go doc        查看文档，如查看 fmt 包中的所有函数 `go doc fmt`，或只看特定函数
                `go doc fmt Println`

在 go 1.12 之后的版本中，godoc 不再做为 go 编译器的一部分存在。
```
C:\Users\T460P>go version
go version go1.13.6 windows/amd64
```

## godoc 命令

可以通过 go get 命令安装：
```
C:\Users\T460P>go get -u -v golang.org/x/tools/cmd/godoc
package golang.org/x/tools/cmd/godoc: unrecognized import path
"golang.org/x/tools/cmd/godoc" (https fetch: Get
https://golang.org/x/tools/cmd/godoc?go-get=1: dial tcp 216.239.37.1:443:
connectex: A connection attempt failed because the connected party did not
properly respond after a period of time, or established connection failed
because connected host has failed to respond.)
```

变量的默认设置：
```
set GO111MODULE=
set GOPROXY=https://proxy.golang.org,direct
```

```
# 启用 Go Modules 功能
PS > $env:GO111MODULE="on"
# 配置 GOPROXY 环境变量
PS > $env:GOPROXY="https://goproxy.io"

PS > go get -u -v golang.org/x/tools/cmd/godoc
```

通过命令行设置代理，命令行窗口关闭后失效：
```
set http_proxy=socks5://127.0.0.1:1087 && go get -u -v golang.org/x/tools/cmd/godoc
```
高级设置，环境变量中设置

在本地建立 Go 官网：
```
godoc -http=:8080
```

## 变量

变量声明：
```go
var m bool
var i int = 10
var s string = "abc"
```
* 使用 var 关键字定义变量
* 变量名在前，变量类型在后，好处之一就是可以多个同类型变量同时声明

* 声明的变量必须使用，否则报错
```go
var i,j,k int = 3,4,5
```

### 变量组

函数体内不可进行一组变量的声明
```go

var (
    name = "cn"
    age = 70
)

func test() {

}
```

## go 程序的一般结构

```go
// 当前包名
package main

// 导入的其他包名
import (
    "fmt"
)

// 常量声明
const (
    PI = 3.14
)

// 全局变量声明
var (
    Nation = "CN"
)

// 一般类型声明
type intType int

// 结构类型声明
type car struct {}

// 接口类型声明
type usb interface {}

// 程序入口
func main() {

}
```

## main 包和 main 函数

main 包和 main 函数是唯一对应的，即 main 包中必须包含一个 main 函数，main 函数也
必须位于 main 包中。
```go
// 没有 main 包 ：
go run: cannot run non-main package

// main 包中没有定义 main 函数：
# command-line-arguments
runtime.main_main·f: function main is undeclared in the main package
```

## import 别名和省略调用

如下面的 fmt 重命名为 io，而 os 包重命名为点，之后调用 os 的函数可以不使用包名，
不建议使用省略调用：
```go
package main

import io "fmt"
import . "os"

func main() {
    io.Println("hello")
    io.Println(Args)
}
```

## 可见性规则

根据约定，go 语言根据首字母的大小写来确定常量、变量、类型、结构、接口、函数的可
见性：
* 首字母小写为 private
* 首字母大写为 public

编码规范为常量全大写，如果是不可见常量，则可以前面加下划线 ???

注意这里的可见性范围指的是以包 package 为单位，即同一个包内都是可见的，只有跨包
后才有可见不可见。???

## 数据类型

### 基本类型

* bool      1 字节  取值 true 和 false，不可用 0 、1 数字等隐式转换

* byte      1 字节  0 ~ 255, uint8 别名
* rune      4 字节  -2^32/2 ~ 2^32/2 - 1，int32 的别名，一般用于 Unicode 操作

* int8      1 字节     -128 ~ 127
* uint8     1 字节        0 ~ 255
* int16     2 字节   -32768 ~ 32767
* uint16    2 字节        0 ~ 65535
* int32     4 字节  -2^32/2 ~ 2^32/2 - 1
* uint32    4 字节        0 ~ 2^32 - 1
* int64     8 字节  -2^64/2 ~ 2^64/2 - 1
* uint64    8 字节        0 ~ 2^64 - 1

* int       根据运行平台，为 4 字节或 8 字节
* uint      根据运行平台，为 4 字节或 8 字节

* float32   4 字节，精确到 7 位小数
* float64   8 字节，精确到 15 位小数，没有 double 型

* complex64     8 字节
* complex128    16 字节

* uintptr       用于保存指针的 32 位或 64 位
* array
* struct
* string

### 复合类型

* slice     切片
* map       类似于哈希表
* chan      通道

* interface 接口类型
* func      函数类型

## 类型零值

零值不等于空值，而是变量被声明为某种类型后的默认值，如 bool 的零值为 false，数值
类型的零值为 0，字符处类型的零值为空字符串，引用类型。

## 类型别名

自定义类型 
```go
type (
    mytype int8
)

func main() {
    var s mytype
    s = 3
    fmt.Println(s)
}
```

类型别名和自定义类型的区别是，别名变量之间可以直接赋值，无需类型转换，但自定义类
型之间赋值需要进行类型转换的，因为自定义类型和原类型只是底层的数据结构相同，其实
是两种类型。

例如 byte 是 uint8 的别名，rune 是 int32 的别名，byte 和 uint8 之间可以直接进行
赋值：
```go
func main() {
    var a uint8 = 12
    var b byte
    b = a
    fmt.Println(b)
}
```

下面的自定义类型和原类型之间就需要类型转换才可以：
```go
type (
    mytype int8
)

func main() {
    var s mytype = 3
    var t int8
    t = s
    // 需要类型转换
    // t = int8(s)
    fmt.Println(t)
}

错误提示：cannot use s (type mytype) as type int8 in assignment
```

## 变量声明

用 var 声明变量
```go
var t string
t = "hello"

var t string = "hello"

var t  = "hello"        // 这种情况将根据赋值推断变量类型
```

还可以省略 var 关键字，并使用类型推断，进行最简短的变量声明赋值：
```go
t := "hello"
```

注意全局变量必须使用 var 关键字声明，不可使用 `:=` 的方式声明

全局变量可以使用变量组一次声明多个变量，但方法体内不可使用变量组声明：
```go
var (
    s string
    t int
)
```

方法体内使用这种并行的方式一次声明多个变量：
```go
func main() {
    var a,b,c,d int
    a,b,c,d = 1,2,3,4
    fmt.Println(a,b,c,d)
}

func main() {
    var a,b,c,d int = 1,2,3,4
    fmt.Println(a,b,c,d)
}

func main() {
    a,b,c,d := 1,2,3,4
    fmt.Println(a,b,c,d)
}
```

全局变量也可以使用并行方式：
```go
var (
    i,j = 5,6
)
```

占位符和 `:=` 常用于函数返回值：
```go
func main() {
    a,b,_,d := 1,2,3,4
    fmt.Println(a,b,d,i,j)
}
```

## 类型转换

go 中不允许隐式转换，所有的转换必须显式进行，转换的格式：
```go
<ValueA> [:]= <TypeOfValueA>(<ValueB>)
```

例如
```go
func main() {
    a := 1.1
    b := int(a)
    fmt.Println(b)
}

func main() {
    a := 1
    b := float32(a)
    fmt.Println(b)
}
```

只能在两种互相兼容的类型之间进行类型转换：
```go
func main() {
    var s byte = 65
    var t string
    t = string(s)
    fmt.Println(t)
}
输出：A

func main() {
    a := true
    b := int(a)
    fmt.Println(b)
}

// 错误提示：cannot convert a (type bool) to type int
```

上面的第一个例子如要输出字符串 65，则需用下面的方法：
```go
import (
	"fmt"
    "strconv"
)

func main() {
    var s int = 65
    var t string
    t = strconv.Itoa(s) // Itoa 接收的参数类型为 int
    fmt.Println(t)
}

// 输出 "65"

func main() {
    var s int
    var t string = "65"
    s,_ = strconv.Atoi(t)
    // s = int(t)
    fmt.Println(s)
}

// 输出 65
```

## 常量

常量的值在编译时就已确定，赋值表达式中必须是常量，可有函数，但必须是内置函数

常量定义只使用 `=` 不能使用 `:=`

**常量的初始化规则**

* 在定义常量组时，不提供初始值，则表示使用上一行的表达式进行初始化
```go
const PI = 3.14
const s string = "abc"
const (
    i,j = len(s), i * 2
    m = 3
    n               // 这里直接使用 m 的值
    k,K = 4,5
    p,P             // p 和 P 分别使用 k 和 K 的值
)

func main() {
    fmt.Println(PI,s,i,j,m,n,k,K,p,P)
}

// 输出
3.14 abc 3 6 3 3 4 5 4 5
```

* iota 常量计数器

每个常量组中从 0 开始，每定义一个常量就加 1，下一个常量组重新从 0 开始计数：
```go
const (
    a = 'A'     // 65
    b           // 65
    c = iota    // 2
    d           // 3
    e = iota    // 4
)

const (
    f = 'A'     // 65
    g           // 65
    h = iota    // 2
)

func main() {
    fmt.Println(a,b,c,d,e,f,g,h)
}
// 输出：65 65 2 3 4 65 65 2
```

利用 iota 实现计算机存储单位的枚举：
```go
const (
    _          = iota
    KB float64 = 1 << (iota * 10)
    MB
    GB
    TB
    PB
    EB
    ZB
    YB
)
```

## 运算符

位运算符：
* & 按位与
* | 按位或
* ^ 按位异或
* &^ 按位置0，如 `A &^ B`，当第二个操作数 B 的位为 1 时，则第一位操作数对应位必须
  为 0， 则 `0110 &^ 1011 = 0100`

注意 go 中的 `++` `--` 是作为语句而不是表达式，因此：
```go
i := 0
i++      // 可以的
++i      // 不可以，只能放变量右侧
j := i++ // 不可以，语句不可赋值给变量
```

## 指针

go 保留了指针，但不支持指针运算符 `->`，而是：
* 使用 `.` 操作指针目标对象的成员
* 使用 `&` 获取变量地址
* 使用 `*` 通过指针间接访问目标对象

指针默认值是 nil 而不是 NULL

```go
func main() {
    v := 3
    var p *int
    fmt.Println(p)          // <nil>
    // fmt.Println(*p)      // panic: runtime error: invalid memory address or nil pointer dereference
    p = &v
    fmt.Println(p)          // 0xc0000120d0
    fmt.Println(*p)         // 3
}
```

## 控制结构

### if 语句

if 语句的：
* 条件表达式**不能**使用括号括起来
* 支持一个初始化表达式，初始化表达式的变量范围为当前的 if else 语句块
* 左大括号必须和条件语句或 else 在同一行

```go
func main() {
    a := 5
    if a := 4; a > 2 {
        fmt.Println(a)
    }
    fmt.Println(a)
}
// 输出 4 5
```

### for 语句

```go
for a := 5; a > 0; a-- {
    fmt.Println(a)
}
// 输出 5 4 3 2 1
```

### switch case 语句

case 语句不需要 `break`，会执行完匹配的代码块自动跳转出来，反而要继续执行其后的
case，则需要 `fallthrough`

没有 case else 语句，而是 default 语句

不可以使用类型或表达式作为条件语句

```go
func main() {
    switch a := 2; a {
        case 1:
            fmt.Println(1)
        case 2:
            fmt.Println(2)
            fallthrough
        case 3:
            fmt.Println("greater than or equal to 2")
    }

    // 输出
    // 2
    // greater than or equal to 2

    b := 2
    switch {
        case b < 1:
            fmt.Println("less than 1")
        case b < 2:
            fmt.Println("less than 2")
        default:
            fmt.Println("less than 3")
    }

    // 输出
    // less than 3
}
```

## 跳转语句

3 个跳转语句：
* goto LABEL        调整程序执行位置，跳到 LABEL 标签标识的代码开始执行
* break [LABEL]     跳出 LABEL 标签所标识的循环
* continue [LABEL]  跳到 LABEL 标签标识的循环开始执行

不同于其他语言，go 语言中的 break 和 continue 后面还可以跟标签，即指定跳转到某个
标签

```go
func main() {
    LABEL1:
    for i:=1; i<5; i++ {
        for {
            fmt.Println(i)
            break LABEL1
        }
    }
}

// 输出
// 1

func main() {
    LABEL1:
    for i:=1; i<5; i++ {
        for {
            fmt.Println(i)
            continue LABEL1
        }
    }
}

// 输出
// 1
// 2
// 3
// 4

func main() {
    LABEL1:
    for i:=1; i<5; i++ {
        for {
            fmt.Println(i)
            goto LABEL1
        }
    }
}

// 输出，一直是 1 无限循环
// 1
// 1
// 1
// .....
```

## 数组

定义格式： `var <VarName> [n]<Type>`，n 指定数组长度， n >= 0，例如：
```go
var names [3]string
```

使用 new 关键字生成一个指向数组的指针
```go
p := new([3]int)
fmt.Println(p)          // &[0 0 0]，注意前面的 & 号，表示 p 是一个数组指针
p[1] = 3
fmt.Println(p)          // &[0 3 0]
```

其他语言中的数组为一种引用类型，go 语言中，数组是一种值类型，可以使用 `==` 和
`!=` 进行比较，不能使用 `<` 和 `>` 进行比较：
```go
c  := [3]int{1,2,3}
d  := [3]int{4,2,3}
e  := [2]int{2,3}
fmt.Println(c==d)       // 输出 false
fmt.Println(c!=d)       // 输出 true
fmt.Println(e!=d)       // 错误，类型不通
```

数组长度也是类型的一部分，因此不同长度的数组，类型不一样，不能互相赋值、比较：
```go
var n [3]int
var o [3]int
var p [2]int
n = o           // 没问题
n = p           // 出错
                // 输出
                // cannot use o (type [2]int) as type [3]int in assignment
```

数组长度 n 也可用三个点替代，这时需要同时指定数组元素，指定了几个数组元素就表示
数组长度为几：
```go
m := [...]int{1,2,3,4,5}
fmt.Println(m)              // 输出 [1 2 3 4 5]
```

数组赋值时可以根据下标指定值：
```go
func main() {
    k := [4]int{3,4}
    fmt.Println(k)          // 输出 [3 4 0 0] 

    j := [4]int{2:7,3:9}    // 下标 3 的元素赋值 9，其他元素使用类型的零值
    fmt.Println(j)          // 输出 [0 0 7 9] 
}
```

下标和三个点也可以结合使用：
```go
n := [...]int{3:7}
fmt.Println(n)              // 输出 [0 0 0 7]
```

数组的指针：
```go
a := [5]int{3:1}
var p *[5]int = &a
fmt.Println(p)              // 输出 &[0 0 0 1 0]
```

指针的数组：
```go
a := [5]int{1,2,3,4,5}
b := [5]*int{&a[0],&a[1],&a[2],&a[3],&a[4]}
fmt.Println(a)              // 输出 [1 2 3 4 5]
fmt.Println(b)              // 输出 [0xc00008a090 0xc00008a098 0xc00008a0a0 0xc00008a0a8 0xc00008a0b0]
```

多维数组：
```go
e := [2][3]int{{1,2,3},{4,5,6}}
f := [...][3]int{{1,2,3},{4,5,6}}   // 第二维不可以用三个点
fmt.Println(e)
fmt.Println(f)
// 输出 [[1 2 3] [4 5 6]]
// 输出 [[1 2 3] [4 5 6]]
```

数组冒泡排序：
```go
a := [6]int{3,9,7,6,1,4}
fmt.Println(a)
num := len(a)
for j := 0; j < num; j++ {
    for i := 0; i < j; i++ {
        if a[i] < a[j] {
            tmp := a[i]
            a[i] = a[j]
            a[j] = tmp
        }
    }
}
fmt.Println(a)
```

## 切片

数组是内存中一块连续的数据，无法改变其占用的空间，而通过切片对数组的引用，可以实
现变长数组的效果，切片的容量参数就是指定为切片初始化分配的内存空间大小，而其长度
参数则是实际存储的元素个数，当要存储的元素个数超过切片容量时，会再次重新为切片分
配一块连续的存储空间，其大小为目前的两倍，每次超过当前容量时，都会按当前容量的2
倍重新分配空间，如一个切片其容量为10，当要存储大于10个元素时，则会重新分配20个元
素的空间，当要存储的元素增加到21时，则再为其重新分配40个元素的空间.....，如此频
繁重新分配空间会导致效率降低，所以切片的容量参数应该设置合理，即数据可能使用到最
大的空间是多少，就分配多大的容量。

`[n]int` 或 `[...]int` 是数组类型，而 `[]int` 是切片类型

数组是值类型，切片是引用类型，它关联了底部数组的局部或全部，可以通过底层数组获取
生成切片，或直接创建切片。

`make([]Type,len,cap)`
* cap 可省略，则和 len 相同
* len 元素个数，对应的函数 len(slice)
* cap 容量，对应的函数 cap(slice)

直接从数组生成切片：
```go
var s []int
a := [10]int{0,1,2,3,4,5,6,7,8,9}
s = a[3]            // cannot use a[3] (type int) as type []int in assignment
fmt.Println(s)      // 这种方式是取出数组单个元素而不是切片
s = a[3:4]
fmt.Println(s)      // [3]
s = a[3:8]
fmt.Println(s)      // [3 4 5 6 7]
s = a[3:]
fmt.Println(s)      // [3 4 5 6 7 8 9]
s = a[:8]
fmt.Println(s)      //[0 1 2 3 4 5 6 7]
s = a[:-2]          // invalid slice index -2 (index must be non-negative)
fmt.Println(s)
s = a[:]
fmt.Println(s)      // [0 1 2 3 4 5 6 7 8 9]
```

切片容量的自动变化：
```go
a := [10]int{0,1,2,3,4,5,6,7,8,9}
fmt.Println(a)          // [0 1 2 3 4 5 6 7 8 9]

s1 := make([]int, 3, 3) // 这里定义切片的容量为 3
fmt.Println(len(s1))    // 3
fmt.Println(cap(s1))    // 3  <-- 容量确实为 3

s1 = a[2:5]             // 对数组切片后，容量自动变化为 8
fmt.Println(len(s1))    // 3
fmt.Println(cap(s1))    // 8
```

### 切片操作

#### Reslice

切片可能存在两种越界：
* 第一是 index out of range：使用 [n] 的方式获取单个元素时，n 大于或等于了切片的
  长度则越界
* 第二是 slice bounds out of range：使用 [m:n] 的方式对切片再次进行切片时，n 大
  于了切片的容量
```go
func main() {
    a := [10]int{0,1,2,3,4,5,6,7,8,9}
    fmt.Println(a)          // [0 1 2 3 4 5 6 7 8 9]

    s1 := make([]int, 3, 3)
    fmt.Println(len(s1))    // 3
    fmt.Println(cap(s1))    // 3

    s1 = a[2:5]
    fmt.Println(len(s1))    // 3
    fmt.Println(cap(s1))    // 8

    fmt.Println(s1[3])      // 报错：index out of range [3] with length 3
                            // 即长度为 3，下标最大为 2
                            // 切片获取单个元素时索引不能超过切片的长度

    fmt.Println(s1)         // [2 3 4]
    fmt.Println(s1[3:8])    // [5 6 7 8 9]
                            // 虽然 s1 长度为 3 只有 3 个元素，但其容量为 8，可
                            // 以对其容量内的数据再次切片
    fmt.Println(s1[3:9])    // slice bounds out of range [:9] with capacity 8
                            // s1 本身就是切片，再对其切片时，不能超过其容量
}
```

#### Append

在切片尾部追加单个元素或另一个切片，这里的切片尾部是指切片的长度，不是容量，当追
加的内容超过切片容量时，会为切片重新分配空间：
```go
s := make([]int, 3, 6)
fmt.Printf("%v---%p\n", s, s)   // [0 0 0]---0xc00000c2d0
s = append(s,3,4,5)
fmt.Printf("%v---%p\n", s, s)   // [0 0 0 3 4 5]---0xc00000c2d0
s = append(s,6,7,8)
fmt.Printf("%v---%p\n", s, s)   // [0 0 0 3 4 5 6 7 8]---0xc0000180c0，这里内存
                                // 地址变化，因为重新分配了空间
```

当追加的内容超过切片容量，重新为切片分配空间后，其关联的底层数组不再是原来的数组
：
```go
a := []int{0,1,2,3}
s := a[0:3]
k := a[0:3]
fmt.Println(cap(s))                 // 4

fmt.Printf("%v,%p\n", a, a)         // 0xc00000a3c0:[0 1 2 3]
fmt.Printf("%v,%p\n", s, s)         // 0xc00000a3c0:[0 1 2]
fmt.Printf("%p:%v\n", k, k)         // 0xc00000a3c0:[0 1 2]

s = append(s,4)
fmt.Printf("%v,%p\n", a, a)         // 0xc00000a3c0:[0 1 2 4] 这里对切片使用 append 更改了底层数组???
fmt.Printf("%v,%p\n", s, s)         // 0xc00000a3c0:[0 1 2 4]
fmt.Printf("%p:%v\n", k, k)         // 0xc00000a3c0:[0 1 2]

s = append(s,5)
fmt.Printf("%v,%p\n", a, a)         // 0xc00000a3c0:[0 1 2 4]
fmt.Printf("%v,%p\n", s, s)         // 0xc00000e280:[0 1 2 4 5] 这里切片重新分配空间，地址更改
fmt.Printf("%p:%v\n", k, k)         // 0xc00000a3c0:[0 1 2]
```

#### Copy

`copy(slice1, slice2)` 把 slice2 中的元素复制到 slice1 中，默认从索引 0 开始替换
，最后长度为 slice1 的长度：
```go
s1 := []int{0,1,2,3,4,5,6}
s2 := []int{7,8,9}
copy(s1, s2)
fmt.Println(s1)                     // [7 8 9 3 4 5 6]

s1 = []int{0,1,2,3,4,5,6}
s2 = []int{7,8,9}
copy(s2, s1)
fmt.Println(s2)                     // [0 1 2]

// 复制时也可通过切片指定范围
s1 = []int{0,1,2,3,4,5,6}
s2 = []int{7,8,9}
copy(s1[2:4], s2[1:])
fmt.Println(s1)                     // [0 1 8 9 4 5 6]
```

第一个变量 `a` 为切片，其他两个都是数组，注意 `cap` 容量也可以测数组：
```go
a := []int{1,2,3,4}
a = append(a,5,6,7,8)

b := [...]int{1,2,3,4}
//b = append(b,5,6,7,8)     // 错误： first argument to append must be slice

c := [4]int{1,2,3,4}
//c = append(c,5,6,7,8)     // 错误：first argument to append must be slice
```

## map

特点：
* 以 `key:value` 形式存储数据，类似其他语言的哈希表或字典
* key 必须是支持 `==` 和 `!=` 比较的数据类型，value 可以为任意类型：函数、map、
    slice 等
* map 搜索比线性查找快很多，但比使用索引访问数据的数组、slice 等类型慢 100 倍

使用 `make(map[keyType]valueType, cap)` 创建，容量 cap 可省略，像 slice 一样，超
过容量会扩容，还支持使用简写方式 `:=` 创建：
```go
var m map[int]string
m = {0:"a", 1:"b", 2:"c"}               // 错误：non-declaration statement outside function body
m = map[int]string{}                    // map[]
m = map[int]string{0:"a", 1:"b", 2:"c"} // map[0:a 1:b 2:c]
fmt.Println(m)

var n map[int]string
n = make(map[int]string)
fmt.Println(n)          // map[]

var o map[int]string = make(map[int]string)
fmt.Println(o)          // map[]

p := make(map[int]string)
p = map[int]string{0:"a", 1:"b", 2:"c"}
fmt.Println(o)          // map[0:a 1:b 2:c] 

p := map[int]string{0:"a", 1:"b", 2:"c"}
fmt.Println(o)          // map[0:a 1:b 2:c] 
```

可以把 `map[keyType]valueType` 如 `map[int]string` 看作是一个类型，即 map 类型


len 获取键值对个数，delete 删除指定键值对
```go
func main() {
    m := make(map[int]string)
    fmt.Println(m[1])           // 输出空，当访问不存在的键值时不出错
    m[1] = "a"
    fmt.Println(m[1])
    fmt.Println(len(m))         // 1
    delete(m, 1)                //
    fmt.Println(m[1])
}
```

map 的 map，即其键对应的值也是一个 map：`map[int]map[int]string`
```go
m := make(map[int]map[int]string)       // 只初始化了最外层的 map
m[1] = make(map[int]string)             // 内层的 map 初始化
m[1][1] = "a"
fmt.Println(m[1][1])                    // a

m := make(map[int]make(map[int]string)) // 不能这样连写
```

map 取值时可返回多个值，可以用于判断是值为类型的零值还是值未初始化：
```go
n := make(map[int]int)

n[1] = 0
v, ok := n[1]
fmt.Println(v)      // 0
fmt.Println(ok)     // true

v, ok = n[2]
fmt.Println(v)      // 0
fmt.Println(ok)     // false
```

`for index, value := range slice` 对切片进行迭代时，其返回的 value 为复制品，
```go
func main() {
    s := []int{3, 4, 5}
    fmt.Println(s)              // [3 4 5]

    for k, v := range s {
        v = v + k
    }
    fmt.Println(s)              // [3 4 5]

    for k, v := range s {       // 因为 k，v 都是块级变量，所以上面声明过了这里
                                // 又要重新声明
        s[k] = v + 1

    }
    fmt.Println(s)              // [4 5 6]
}
```

`for key, value := range map` 对 map 进行迭代时，其返回的 value 也为复制品，
```go
func main() {
    m := make([]map[int]string, 5)  // 元素为 map 的切片：[]map[int]string
    for _, v := range m {
        v = make(map[int]string, 1)
        v[1] = "ok"
        fmt.Println(v)
    }
    fmt.Println(m)
}

// 输出
map[1:ok]
map[1:ok]
map[1:ok]
map[1:ok]
map[1:ok]
[map[] map[] map[] map[] map[]]
```

map 是无序的，通过对 map 键的排序，实现对 map 的间接排序：
```go
import (
	"fmt"
    "sort"
)

func main() {
    m := map[int]string{1:"a", 2:"b", 3:"c", 4:"d"}
    s := make([]int, 5)
    i := 0
    for k, _ := range m {
        s[i] = k
        i++
    }
    sort.Ints(s)            // 没有这行，则每次输出的顺序都不一样
    fmt.Println(s)          // [0 1 2 3 4]
}
```

## 函数

Go 函数也是一种数据类型

Go 函数支持：
* 无需声明原型
* 不定长度变参
* 多个返回值
* 命名返回值
* 匿名函数
* 闭包

Go 函数不支持：
* 嵌套
* 重载
* 默认参数

命名返回值：
```go
func main() {
    fmt.Println(test1())            // 1 2 3
    fmt.Println(test2())            // 1 2 3
}

func test1() (int, int, int) {
    a, b, c := 1, 2, 3
    return a, b, c
}

func test2() (a, b, c int) {        // 命名返回值是函数的内部变量
    a, b, c = 1, 2, 3               // 这里不能再写为 :=
    return
}
```

在参数名和类型之间加三个点，则参数变为一个切片，即为不定长变参，其必须作为参数列
表最后一个参数：
```go
func main() {
    test(0,1,2,3)                   // [1 2 3 99]
    test(0,1,2,3,4,5)               // [1 2 3 4 5 99]
}

func test(a int, b...int) {
    b = append(b, 99)
    fmt.Println(b)
}
```

不定长变参为切片，而切片为引用类型，但注意不是在调用函数的地方作为切片传递过去的
，而是在函数声明的地方变为切片，所以和直接传递切片变量过去有区别：
```go
func main() {
    a, b := 1,2
    test1(a, b)
    fmt.Println(a,b)

    s := []int{1, 2}
    test2(s)
    fmt.Println(s)
}

func test1(b...int) {
    b[0] = 3
    b[1] = 4
    fmt.Println(b)
}

func test2(b []int) {
    b[0] = 3
    b[1] = 4
    fmt.Println(b)
}

// 输出
[3 4]
1 2
[3 4]
[3 4]
```

值变量的指针传递
```go
func main() {
    d := 6
    test3(&d)
    fmt.Println(d)      // 9
}

func test3(i *int) {
    *i = 9
}
```

Go 函数也是一种数据类型
```go
func main() {
    a := test
    a()             // hello
}

func test() {
    fmt.Println("hello")
}
```

匿名函数即不需要指定名称的函数，可通过变量调用：
```go
func main() {
    a := func() {
        fmt.Println("world")
    }
    a()             // world
}
```

匿名函数也可定义时调用
```go
func main() {

    func() {
        fmt.Println("hello")
    }()

}

匿名函数不能作为顶层函数，只能位于其他函数体内：
```go

```

返回函数的函数即为闭包函数：
```go
func main() {
    a := closure(10)
    fmt.Println(a(1))       // 11
    fmt.Println(a(2))       // 12
    fmt.Println(a(3))       // 13
}

func closure(x int) func (int) int{
    fmt.Printf("%p\n", &x)          // 这输出1次 0xc0000120e0
    return func (y int) int {
        fmt.Printf("%p\n", &x)      // 这里输出3次 0xc0000120e0
        return x + y
    }
}
```

## defer

按调用顺序的相反顺序执行：
```go
func main() {
    for i := 0; i < 3; i++ {
        fmt.Println("a", i)
        defer fmt.Println("b",i)
    }
}

// 输出
a 0
a 1
a 2
b 2
b 1
b 0
```

defer 支持匿名函数的调用：
```go
func main() {
    i := 1
    fmt.Println(1, i)
    i++
    defer func() {
        fmt.Println(2, i)
        i++
    }()
    fmt.Println(3, i)
    i++
}

//输出
1 1
3 2
2 3
```

循环内调用 defer 时注意变量的值：
```go
func main() {
    for i := 0; i < 3; i++ {
        func() {
            fmt.Println(i)
        }()
    }
}
// 输出
0
1
2


func main() {
    for i := 0; i < 3; i++ {
        func() {
            defer fmt.Println(i)
        }()
    }
}
// 输出
0
1
2

func main() {
    for i := 0; i < 3; i++ {
        defer func() {
            fmt.Println(i)
        }()
    }
}
// 输出
3
3
3
```

defer 调用在函数 return 之后执行，因此 defer 与匿名函数结合可在 return 之后修改
函数计算结果：
```go

```

Go 没有异常机制，它使用 panic/recover 模式来处理错误，panic 可以在任何地方引发：
```go
func main() {
    A()
    B()
    C()
}

func A() {
    fmt.Println("func A executed")
}


func B() {
    panic("func B panic")
    fmt.Println("func B executed")
}

func C() {
    fmt.Println("func C executed")
}

// 输出
func A executed
panic: func B panic

goroutine 1 [running]:
main.B(...)
        C:/Users/T460P/test.go:19
main.main()
        C:/Users/T460P/test.go:9 +0x9d
exit status 2
```

但 recover 只在 defer 调用的函数中有效，因为 defer 可以确保任何情况下都会被执行
，下面是使用 defer、panic、recover 的演示，通过 defer 语句，把处于 panic 状态的
程序 recover 回来，只修改上面的函数 B：
```go
func B() {
    defer func() {
        if err := recover(); err != nil {
            fmt.Println("func B recovered")
        }
    }()

    panic("func B panic")               // 注意 panic 语句必须位于 defer 之后
    fmt.Println("func B executed")      // 这句始终没执行
}

// 输出
func A executed
func B recovered
func C executed
```

defer 调用即使在函数发生严重错误时也会执行，因此常用于资源清理、文件关闭、解锁以
及记录时间等操作：
```go

```

defer 和闭包、匿名函数例子1???
```go
func main() {
    for i := 0; i < 3; i++ {
        fmt.Println("a", i)
        defer fmt.Println("a",i)    // defer 在定义时就获取了值的拷贝，这里的循
        // 环中的 defer 就是在每次循环时获取 i 值 0 1 2，然后传递给后面的定义
        defer fmt.Println("c",i)    // 这里的 i 是参数，会复制值后传递
        defer func(){fmt.Println("d", i)}()     // 调用匿名函数这里的 i 是引用
        defer fmt.Println("")
    }
}

// 输出
a 0
a 1
a 2

d 3
c 2
b 2

d 3
c 1
b 1

d 3
c 0
b 0

```

defer 和闭包、匿名函数例子2???
```go
func main() {
    a := [4]func(){}

    for i := 0; i < 4; i++ {
        a[i] = func(){fmt.Println(i)}   // 这里的 i 不是通过参数的值拷贝传递的，
        // 而是直接使用的 main 函数中的 i，所以其是通过引用地址的方式访问 i
    }

    //fmt.Println(i)       // 这里提示 undefined: i

    for _, f := range a {
        f()
    }
}
// 输出
4
4
4
4
```

## struct 结构

Go 没有 class，只有 struct：
```go
type human struct {
    height int
    name string;         // 分号可有可无
    age int
}

func main() {
    h0 := human{}
    fmt.Println(h0)     // {0 0}

    h1 := human{name:"lin", age:10, }   // 最后的逗号可有可无
    fmt.Println(h1)     // {0 lin 10}

    h2 := human{
        name:"yan",
        age:30,        // 注意这里最后的逗号必须有，否则报错 unexpected newline,
                       // expecting comma or }
    }
    fmt.Println(h2)    // {0 yan 30}
}
```

struct 是值类型，还可以进行 `==` 和 `!=` 比较，但不能进行 `<` 和 `>` 比较：
```go
type human struct {
    age int
    name string
}
                            // 两者虽然结构相同，但名称不同，是不同的类型
type human2 struct {
    age int
    name string
}

func main() {
    p1 := human{age:10, name:"shen"}
    p2 := human{age:11, name:"shen"}
    p3 := human2{age:11, name:"shen"}
    fmt.Println(p1 == p2)       // false
    fmt.Println(p1 == p3)       // 类型不同不能比较 (mismatched types human and human2)
}
```

struct 是值类型，通过值拷贝传递参数：
```go
func main() {
    h1 := human{name:"lin", age:10, }
    fmt.Println(h1)
    A(h1)
    fmt.Println(h1)
}

func A(h human) {
    h.age = 40
    fmt.Println("func A:",h)
}

// output
{0 lin 10}
func A: {0 lin 40}
{0 lin 10}
```

但值传递效率较底，较多使用 struct 的引用传递：
```go
func main() {
    h1 := &human{name:"lin", age:10,}   // 推荐在变量定义处使用 &，则以后所有
                                        // 用到 h1 的地方都是引用
    fmt.Println(h1)
    A(h1)                               // 不推荐在调用处使用 & 写作 A(&h1)
    h1.name = "alin"                    // go 的优化，不必写作 *h1.name = "alian"
    fmt.Println(h1)
}

func A(h *human) {                      // 接收参数的地方仍然要使用 * 号
    h.age = 40
    fmt.Println("func A:",h)
}

//output
&{0 lin 10}
func A: &{0 lin 40}
&{0 alin 40}
```

struct 支持匿名字段：
```go
type human struct {
    int
    string
}

func main() {
    p := person{10, "shen"}     // 严格按照结构字段的声明顺序
    fmt.Println(p)
}
```

struct 支持匿名结构：
```go
func main() {
    s := &struct {          // 注意这里也是通过 & 获取指向结构的指针
        age int
        name string
    }{
        age:2,
        name:"ahui",
    }
    fmt.Println(s.age)
    fmt.Println(s)
}

//output
2
&{2 ahui}
```

struct 可以嵌入，看起来像继承，但不是继承：
```go
type human struct {
    age int
    name string
}

type habits struct {
    run int
    jump int
}

type teacher struct {
    sex int
    hb habits // 嵌入结构，有字段名称
    human     // 嵌入结构，无字段名称，则其字段名称默认为结构名称，即 human human
    contact struct {    // 匿名嵌入结构，contact 是类型名，不是字段名
        addr string
        phone string
    }
}

func main() {
    t := teacher{
        sex: 1,

        // 有字段名嵌入结构初始化方法
        hb: habits{run:1},
        //jump: 1,
        // unknown field 'jump' in struct literal of type teacher

        // 无字段名嵌入结构初始化方法
        human: human{name:"shen", },
        //age: 13,
        // cannot use promoted field human.age in struct literal of type teacher 

        // 匿名结构不能在这里初始化
        //contact: contact{phone:"999"},
        // undefined: contact

        //phone: "999",
        //  unknown field 'phone' in struct literal of type teacher
    }

    t.hb.jump = 0
    // t.jump = 0 // t.jump undefined (type teacher has no field or method jump)

    t.human.age = 11
    t.age = 11

    t.contact.addr = "beijing"
    //t.addr = "beijing" // t.addr undefined (type teacher has no field or method addr)

    fmt.Println(t)
}
```

嵌入结构时可以有重名，但调用时若模糊不清则会报错：
```go
type O struct {
    A
    B
}

type A struct {
    name string
}

type B struct {
    name string
}

func main() {
    h := O{A:A{name:"A"}, B:B{name:"B"},}
    fmt.Println(h.A.name)   // 正确输出 A
    fmt.Println(h.B.name)   // 正确输出 B
    // fmt.Println(h.name)  // 只有这里模糊不清的名字报错 ambiguous selector h.name
}

```

同名时先获取最外层结构名称：
```go
type O struct {
    name string
    A
    B
}

type A struct {
    name string
}

type B struct {
    name string
}

func main() {
    h := O{name:"O", A:A{name:"A"}, B:B{name:"B"},}
    fmt.Println(h.A.name)   // A
    fmt.Println(h.B.name)   // B
    fmt.Println(h.name)     // O
}
```

## method 方法

Go 的方法是函数的语法糖，函数增加接收者即变为方法，而实际 Go 方法的第一个参数就
是接收者。

函数定义时增加接收者即变为方法：
```go
func (r Receiver)MethodName() {}
```

接收者为结构类型的变量，注意下例中方法 test1 和 test2 的唯一区别，就是使用的是默
认的值传递还是通过指针，所以 Receiver 遵守参数传递规则，可以是值类型或者指针类型
，另外使用指针调用方法时，也无需使用 `*` 符号，和通过指针调用字段是一样的，可以
使用值或指针来调用方法，编译器会自动完成转换
```go
type A struct {
    name string
}

func (a A)test1() {
    a.name = "11"
    fmt.Println("struct A method 1")
}

func (a *A)test2() {
    a.name = "22"
    fmt.Println("struct A method 2")
}

func main() {
    s := A{name:"00"}
    s.test1()           // 注意这里无需使用 `*s.test1()` 这种方式
    fmt.Println(s)      // {00}，值传递，修改字段失败
    s.test2()
    fmt.Println(s)      // {22}
}
```

底层类型可以通过别名实现方法绑定：
```go
type A int

func (a *A)test() {
    fmt.Println("type A method 1")
}

func main() {
    var s A
    s.test()        // 这种调用称为 Method Value
    (*A).test(&s)   // 这种调用类似于类的静态方法，直接由类型调用，称为 Method Expression
}
```
由此看出类型别名 A 有了新方法，这个新方法不一定适合原来的 int 类型，所以类型 A
和类型 int 之间不能直接进行赋值，需要强制转换。

类型 A 也不会拥有底层类型 int 所附带的方法???

Go 方法没有重载，即一个类型绑定的方法名不能重复，不同类型的方法名可以重复：
```go
type A int
type B int

func (a *A)test() {
    fmt.Println("type A method 1")
}

func (a *A)test(i int) {            // 错误 method redeclared: A.test，
                                    // 若 *A 改为 *B 则没问题
    fmt.Println("type A method 1")
}

func main() {
    var s A
    s.test()
}
```

方法绑定只能在同一个包中进行，即方法和类型必须在同一个包中。

和字段一样，如果外部结构和嵌入结构存在同名方法，则优先找到外部结构方法，优先调用
外部结构的方法

上面的方法名都是小写，按 Go 的访问规则都是私有字段，而方法仍然可以访问，是因为私
有是针对其他包而言，同一个包中不存在私有公有。

练习：为底层类型 int 增加一个方法，实现调用后加 100
```go
type MyInt int

func main() {
    var o MyInt         // 这里写为 o := 0 则为 int 类型，或者写为 o := MyInt(0)
    o.add(100)
    fmt.Println(o)
}

func (a *MyInt) add(num int) {
    *a += MyInt(num)            // 注意这里必须是 *a， 而不是 a，因为 a 是个内存
                                // 地址，无法和后面的数值型相加
}
```

## interface 接口

接口就是一个协议, 规定了一组成员
接口的成员就是一个或多个方法签名
接口的方法只有声明，没有实现
接口没有数据字段

方法签名不需要 `func` 关键字
```go
type interface_name interface {
    func1_signature
    func2_signature
    ....
}
```

Go 的接口实现无需显式声明：
只要某个类型拥有该接口的所有方法签名，即算实现该接口，无需显式声明实现了哪个接口
，这称为 Structural Typing
```go
import (
	"fmt"
    "strconv"
)

type USB interface {
    powerConnect() string
    dataConnect(current int) string
}

type PC struct {
    voltage int
}

// ???
// 下面的改为指针接收器 *PC，则提示错误 ：
// PC does not implement USB (powerConnect method has pointer receiver)
func (p PC) powerConnect() string{
    return "pc is plugged " + strconv.Itoa(p.voltage) + "v power"
}

func (p PC) dataConnect(c int) string{
    return "pc has " + strconv.Itoa(c) + " USB"
}

func main(){
    var u USB
    u = PC {555555}

    // 只能这样赋值，改为 u.voltage = 5 这种赋值方法，则提示：
    // u.voltage undefined (type USB has no field or method voltage)
    fmt.Println(u.powerConnect())
    fmt.Println(u.dataConnect(3))
    disconnect(u)
}

func disconnect(p USB) {
    fmt.Println("disconnected")
}

//output
pc is plugged 555555v power
pc has 3 USB
disconnected
```

### 嵌入接口

接口可以匿名嵌入其它接口，或嵌入到结构中

将上面的一个接口：
```go
type USB interface {
    powerConnect() string
    dataConnect(current int) string
}
```
改为：
```go
type USB interface {
    USBPower
    dataConnect(current int) string
}

type USBPower interface {
    powerConnect() string
}
```

### 类型断言

```go
func disconnect(p USB) {
    fmt.Println("disconnected")
}
```
上面的 disconnect 方法接收的是 USB 类型，可以在断开时判断一下其是否为实现了 USB
的 PC 类型，如下所示 `pc, ok := usb.(PC)` 即为类型断言：
```go
func disconnect(usb USB) {
    if pc, ok := usb.(PC); ok {
        fmt.Println(pc.voltage, "v  power disconnected")
        return
    }
    fmt.Println("Unknown device")
}
```

### 空接口

**空接口可以作为任何类型数据的容器**

因为 Go 的接口实现规则就是无需显式声明，只要某个类型拥有该接口的所有方法签名，即
算实现该接口，而空接口中无任何方法，则所有的结构都实现了空接口，这个空接口就相当
于其他语言中最顶层的 Object 对象：
```go
type empty interface {
}
```

用上面的 disconnect 方法举例，其接收类型改为空接口，即表示可接收任何类型的数据：
```go
func disconnect(usb interface{}) {
    if pc, ok := usb.(PC); ok {
        fmt.Println(pc.voltage, "v  power disconnected")
        return
    }
    fmt.Println("Unknown device")
}
```

### Type Switch

若接收类型改为空接口，则类型断言更适合 type switch：
```go
func disconnect(usb interface{}) {
    switch v := usb.(type) {
        case PC:
            fmt.Println(v.voltage, "v  power disconnected")
        default:
            fmt.Println("unknown device")
    }
}
```

注意上面的 type switch，`usb.(type)` 必须放在 switch 里，否则报错： `use of
.(type) outside type switch`，下面这样使用是不行的：
```go
    switch v := usb.(type); v {
```
这样输出也不行：
```go
    fmt.Println(usb.(type))
```

通过类型断言的 `ok pattern` 可以判断接口中的数据类型

使用 `type switch` 则可针对空接口进行比较全面的类型判断

### 接口转换

接口也是一种类型，也可以进行转换，但只能结构 Teacher 向接口 Person 转，前提是
Teacher 实现了 Person 的所有方法：
```go
type USBPower interface {
    powerConnect() string
}

type PC struct {
    voltage int
}

func (p PC) powerConnect() string{
    return "pc is plugged " + strconv.Itoa(p.voltage) + "v power"
}

func main() {
    var p PC
    p = PC{5}

    var r USBPower
    r = USBPower(p)
    fmt.Println(r.powerConnect())
}
```

注意，上面的操作是将对象赋值给接口，此时会发生拷贝，而接口内部存储的是指向这个复
制品的指针，既无法修改复制品的状态，也无法获取指针
```go
func main(){
    var p PC
    p = PC{5}

    var r USBPower
    r = USBPower(p)                 // 这里是个复制品
    fmt.Println(r.powerConnect())

    p.voltage = 10
    fmt.Println(r.powerConnect())
}

// output
pc is plugged 5v power
pc is plugged 5v power
```

### 接口 nil

只有当接口存储的类型和对象都为 nil 时，接口才等于 nil
```go
func main(){
    var f interface{}
    fmt.Println(f == nil)

    var i *int = nil
    f = i
    fmt.Println(f == nil)
}

// output
true
false
```

### 匿名字段方法

接口同样支持匿名字段方法

和调用结构方法不同，接口调用不会做receiver的自动转换，方法集的问题 ???

接口也可实现类似OOP中的多态


## reflection 反射

反射可大大提高程序的灵活性，使得 interface{} 有更大的发挥余地
反射使用 TypeOf 和 ValueOf 函数从接口中获取目标对象信息
反射会将匿名字段作为独立字段（匿名字段本质）
想要利用反射修改对象状态，前提是 interface.data 是 settable，
即 pointer-interface
- 通过反射可以“动态”调用方法

```go
import (
	"fmt"
    "reflect"
)

type User struct {
    id int          // reflect 包读取这个包的字段，则遵循可见性规则
                    // 这里改为小写字母开头，则读取非导出字段会报错
                    // panic: reflect.Value.Interface: cannot return value
                    // obtained from unexported field or method
    Name string
    Age int
}

func (u User) Say() { // 这里的方法名改为小写字母开头，reflect 遍历读取时不报错
    fmt.Println(u.Name, "say hello")
}

func (u User) Run() {
    fmt.Println(u.Name, "is running")
}

func main() {
    user := User{12, "shen", 30}
    info(user)
    info(&user)
}

func info(t interface{}) {
    o := reflect.TypeOf(t)
    fmt.Println("Type: ", o.Name())

    // 防止传入类型错误
    // 像 info(&user) 这种调用方法，reflect 会报错
    if k := o.Kind(); k != reflect.Struct {
        fmt.Println("Wrong Type")
        return
    }

    v := reflect.ValueOf(t)
    fmt.Println("Field Values: ", v)

    for i:=0; i<o.NumField(); i++ {
        fmt.Println("Field Name: ", o.Field(i).Name, "Field Type: ", o.Field(i).Type, "Field Value: ", v.Field(i).Interface())
    }

    for i:=0; i<o.NumMethod(); i++ {
        fmt.Println("Method Name: ", o.Method(i).Name, "Method Type: ", o.Method(i).Type)
    }
}

//output
Type:  User
Field Values:  {12 shen 30}
Field Name:  Id Field Type:  int Field Value:  12
Field Name:  Name Field Type:  string Field Value:  shen
Field Name:  Age Field Type:  int Field Value:  30
Method Name:  Run Method Type:  func(main.User)
Method Name:  Say Method Type:  func(main.User)
Type:
Wrong Type
```

### 嵌入结构的反射

反射通过索引获取匿名嵌入结构
```go
type User struct {
    Id int
    Name string
    Age int
}

type Manager struct {
    User
    title string
}

func main() {
    m := Manager{User:User{12, "shen", 30}, title:"manager"}
    t := reflect.TypeOf(m)

    fmt.Printf("%#v\n", t.Field(0))
    fmt.Printf("%#v\n", t.FieldByIndex([]int{0, 0})) // 0,0 表示 Manager 的第一
                                                     // 个成员的第一个成员
}

//output
reflect.StructField{Name:"User", PkgPath:"", Type:(*reflect.rtype)(0x4c0660), Tag:"", Offset:0x0, Index:[]int{0}, Anonymous:true}
reflect.StructField{Name:"Id", PkgPath:"", Type:(*reflect.rtype)(0x4ae7c0), Tag:"", Offset:0x0, Index:[]int{0}, Anonymous:false}
```

### 通过反射修改值

```go
x := 123
v := reflect.ValueOf(&x)
v.Elem().SetInt(999)
fmt.Println(x)

//output
999
```

00:19:00

## concurrency 并发

很多人都是冲着 Go 大肆宣扬的高并发而忍不住跃跃欲试，但其实从
源码的解析来看，goroutine 只是由官方实现的超级“线程池”而已。
不过话说回来，每个实例 4-5KB 的栈内存占用和由于实现机制而大幅
减少的创建和销毁开销，是制造 Go 号称的高并发的根本原因。另外，
goroutine 的简单易用，也在语言层面上给予了开发者巨大的便利。

并发不是并行：Concurrency Is Not Parallelism
并发主要由切换时间片来实现“同时”运行，在并行则是直接利用
多核实现多线程的运行，但 Go 可以设置使用核数，以发挥多核计算机
的能力。

Goroutine 奉行通过通信来共享内存，而不是共享内存来通信。

主协程先退出，导致子协程还没来得及执行就退出：
```go
func main() {
   go LetsGo()
}

func LetsGo() {
    fmt.Println("lets go,lets do it")
}
```
运行上面的代码可以发现，去掉 go 之后会正常输出，加上 go 之后则没有输出，这是因为
go 语句创建了一个子协程 GoRoutine，则 LetsGo 在新创建的协程中运行，而 main 函数所
在的主协程在调用 LetsGo 之后马上就结束退出了，则 LetsGo 所在的子协程也会立即结束

可以让 main 程序暂停运行几秒，等待另一个线程的 LetsGo 输出：
```go
import (
	"fmt"
    "time"
)

func main() {
   go LetsGo()
   time.Sleep(2 * time.Second)  // 这里最好不要直接写 2，而是用 time 给的单位
                               // time.Second 乘上你要的秒数
}

func LetsGo() {
    fmt.Println("lets go,lets do it")
}
```

或者 main 程序让出自己的时间片，让子协程先执行完，然后主协程再执行：
```go

```

但这样通过在暂停主程序执行的方法不完美，正确的方法应该是 LetsGo 运行完后告诉
main 函数，main 函数接到通知后再退出，这就是 channel 的作用，即让两者可以通信
，使用 make 创建 channel：`make(chan int)`, int 代表要存储的数据类型，注意中间没
有逗号：
```go
var c chan bool         // chan 类型变量声明，后面必须有要存储的数据类型，如 bool

func main() {
    c = make(chan bool) // 注意这里没有逗号
    go LetsGo()
    <- c
}

func LetsGo() {
    fmt.Println("lets go,lets do it")
    c <- true
}
```

使用匿名方法可以简化上面代码：
```go
func main() {
    c := make(chan bool)
    go func() {
        fmt.Println("lets go,lets do it")
        c <- true
        // close(c)     执行完主程序退出，可以不关闭
    }()
    <-c
}
```

??? 什么情况下
使用 for range 迭代读取 channel 的值：
```go
func main() {
    c := make(chan bool)
    go func() {
        fmt.Println("lets go,lets do it")
        c <- true
        close(c)    // 必须关闭，否则提示 all goroutines are asleep - deadlock!
    }()

    for v := range c {
        fmt.Println(v)
    }
}
```

### 缓冲

阻塞: 在执行过程中暂停，以等待某个条件的触发，我们就称之为阻塞

Receivers always block until there is data to receive.
无论是否有缓冲，没有数据可接收时，接收方总是在等待接收数据

If the channel is unbuffered, the sender blocks until the receiver has received the value.
无缓冲 channel，发送方总是在等待接收方接收数据。

If the channel has a buffer, the sender blocks only until the value has been copied to the buffer; 
if the buffer is full, this means waiting until some receiver has retrieved a value.
有缓冲 channel，发送方总是在等待数据被复制到缓冲区，如果缓冲区满了，则等待某个接收方取走一个数据。

有缓冲的先放后取
无缓冲的先取后放

有缓存的 channel 是异步的，发送方会一直阻塞直到数据被拷贝到缓冲区；如果缓冲区已
满，则发送方只能在接收方取走数据后才能从阻塞状态恢复。

无缓存的 channel 是同步阻塞的，接收方会一直阻塞直到有数据到来，发送方会一直阻塞
直到接收方将数据取出。

## Channel

Channel 是 goroutine 沟通的桥梁，大都是阻塞同步的
通过 make 创建，close 关闭
Channel 是引用类型
可以使用 for range 来迭代不断操作 channel
可以设置单向或双向通道
可以设置缓存大小，在未被填满前不会发生阻塞

### Select

可处理一个或多个 channel 的发送与接收
同时有多个可用的 channel时按随机顺序处理
可用空的 select 来阻塞 main 函数
可设置超时
