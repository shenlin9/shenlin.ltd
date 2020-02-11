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

## 变量组

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
t = hello

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
        defer fmt.Println("a",i)
        defer fmt.Println("c",i)
        defer func(){fmt.Println("d", i)}()
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
        a[i] = func(){fmt.Println(i)}
    }

    //fmt.Println(i)       // 取消注释则这里提示 undefined: i

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

```go

```
