---
title: Go - IDE - JetBrains, GoLand
categories:
- Go
tags:
- Go
- JetBrains
-  GoLand
date: 2020-02-09 10:14:04
---

Go - IDE - JetBrains, GoLand

<!--more-->

## Action

`ctr + shift + a` 快捷查找编辑器相关命令：
* 如显示编辑器的关于对话框，输入 `about`，
* 如编辑器是否显示行数，输入 `show line`
* 如显示 vim 相关插件，输入 `vim`
使用 `ESC` 退出窗体

`ctrl + /` 注释
* 可以单行或多行注释，多行注释需先选中
* 在注释掉某语句后，如果某个包变为未被使用，则编辑器会自动删除对应的 import 包语
    句，取消注释后，会自动添加此包的引用。

## Completion

在你调用某个包的某个方法后，会自动添加对应的 import 包语句

**基本的自动完成**

`ctrl + 空格`
打开自动完成，回车确认选中项

**类型过滤的自动完成**

`ctrl + shift + 空格`
使用智能类型过滤的自动完成，这时只显示适用于当前上下文环境的自动完成项

**类似方法的自动完成**

`ctrl + 空格` 按两次：
一般都是先输入方法名，然后会自动提示你需要的参数类型，但还可以反过来，先输入一个
参数，如一个字符串 `"hello world"`，然后输入一个点，即 `"hello world."`，接着连
续按两次 `ctrl + 空格`，将会列出一个方法列表，这里的方法都是可以把字符串 `"hello
world"` 作为其第一个参数的方法，选中回车后将自动把字符串 `"hello world"` 放入其
第一个参数位置，若知道方法名，可以继续输入以过滤列表数，注意编辑器会自动补上包名。

**后缀自动完成**

可以减少光标的移动，如要输入 `sort.Strings(message)`，结果先输入了 `message` 则
可以接着输入 `.` ，然后会列出方法列表，选择 `sort` 方法，则自动补全为
`sort.Strings(message)` (类似于上面的两次 ctrl 空格，不过这里不需要按，这里是变
量，上面的是)

**tab 替换**

`fmt.printf(s)` 想要替换为 `println`，则光标移动到 `printf` 上，然后按 `ctrl +
空格`，出现自动完成的可选列表，然后选 `println`，这时回车会插入，按 tab 则会替换
`printf` 为 `println`

**重命名标识符**

移动光标到变量上，然后按 `shift + F6`，则可以为此变量重新输入名字，输入完后回车
确认，所有位置的变量名都会随之改变

**从表达式提取变量**
如想把：
```go
fmt.Println("here is a string")
```
改写为：
```go
s : = "here is a string"
fmt.Println(s)
```
则光标移动到 `"here is a string"` 上，然后按 `ctrl + alt + v`，则会弹出列表让你
确认要把哪个替换为变量，选中确认后会自动改写为下面的两行，并让你输入变量名字，输
入完后回车或 tab 确认。

**移除多余的变量**

和上面的从表达式提取变量过程刚好相反，快捷键是 `ctrl + alt + n`，注意光标需移动
到变量上

**封装代码到方法/函数**

可以把一部分代码封装为一个方法/函数，参数和返回值由你定义，单行代码时移动到代码
行尾，多行时选中，然后使用快捷键 `ctrl + alt + m`，会自动封装为方法。

**规范代码格式**

快捷键 `ctrl + alt + L`，(L小写，这里大写是为了避免混淆)，可以规范化代码格式，若
选中了部分代码则规范选中的代码，若没有选中，则规范整个文件的代码。

`ctrl + shift + alt + L` 会显示规范代码的选项窗口。

`ctrl + shift + alt + f` 会使用 `go fmt` 来规范化代码 ???没有测试成功

**自动补全实现缺失的方法**

可以使用快捷键 `F2` 在文件的错误处跳转，若错误提示为 `some methods are missing`
，则说明调用了还未实现的方法，这时使用快捷键 `alt + enter`，在弹出列表中选
`implement missing methods`，则会自动补全缺失方法

**填充结构字段**

如下代码：
```go
type Address struct {
	street string
}

type Person struct {
	name string
	age int
	address Address
}


func main() {
	p := Person{}
	fmt.Println(p)
}
```
将光标定位到 `p := Person{}` 大括号中间，按 `alt + enter`，弹出列表选 `fill all
fields`，则变为：
```go
	p := Person{
		name:    "",
		age:     0,
		address: Address{},
	}
```

**去除多余的类型转换**

如下所示当你把一个字符串 `getData()` 再使用类型转换 `[]byte` 转换为字符串时：
```go
func main() {
	_ = ioutil.WriteFile("./out.txt", []byte(getData()), 0644)
}
```
将光标定位到 `[]byte`，然后按 `alt + enter`，选择 `Delete conversion`

**删除无用的参数**

如下代码：
```go
type gophersGreeter struct {
	how string
	who string
}

func (greeter gophersGreeter) greet(how string, who string) {
	fmt.Printf("%s %s!", greeter.how, greeter.who)
}
```

将光标定位到参数位置，按 `alt + enter`，选择 `Delete all unsed parameters`
```go
func (greeter gophersGreeter) greet() {
	fmt.Printf("%s %s!", greeter.how, greeter.who)
}
```

或者它们的名字没有使用到，但类型使用到，则可以选择 `Delete parameter names`，变
为：
```go
func (greeter gophersGreeter) greet(string, string) {
	fmt.Printf("%s %s!", greeter.how, greeter.who)
}
```

**更改参数声明的长短类型**

如果方法或函数的参数有多个，且为同一个类型，则有长短两种声明格式：
```go
func greet(how, who, what, when string) {
	fmt.Printf("%s %s!", how, who)
}

func greet(how string, who string, what string, when string) {
	fmt.Printf("%s %s!", how, who)
}
```
光标定位到参数处，按下 `alt + enter`，分别选择 `Reuse signature types` 和
`Expand signature types` 可在这两种格式间切换。

**格式化字符串参数**

光标移动到字符串上，然后按快捷键 `alt + enter`，会出现可用操作的列表，如下面的代
码要增加输出 `subj.id`：
```go
type subject struct {
	id   int
	name string
}

func main() {
	subj := subject{1, "world"}
	fmt.Printf("hello %s ", subj.name)
}
```
则光标移动到字符串 `"hello %s #"`，然后按下 `alt + enter`，在弹出的列表中选 `Add
format string argument`，会提示你输入变量名，输入 `subj.id` 回车，则直接格式化为
：
```go
fmt.Printf("hello %s %d", subj.name, subj.id)
```

**后缀完成模板**

可以使用一系列预定义的后缀自动完成模板，将其应用到已输入的表达式，如下面的函数要
返回 3，可以在先输入 3 后再输入 `.retu`，接着弹出列表选 return 模板，则自动变为
`return 3`：
```go
func test() int{
	return 3
}
```

再例如下面的代码要判断 err 是否为 nil：
```go
file, err := os.Open("example.txt")
```
则先输入 `err`，再输入点和 `nn`，即 `err.nn`，然后弹出的列表选 `nn` 对应的模板，
则自动变为：
```go
if err != nil {

}
```

再例如下面代码：
```go
    file.Write(data).
```
后面输入 `rr`，选 `rr` 模板则变为：
```go
	if _, err := file.Write(data); err != nil {
		return 0
	}
```

再例如下面代码：
```go
    func filter(lines []string) {
        lines.
    }

```
在后面输入 `forr`，选 `forr` 模板则变为：
```go
	for i, line := range lines {

	}
```

**从返回类型创建函数**

如下代码，缺少对应的函数返回 handler：
```go
func main() {
	http.HandleFunc("/", handler)
}
```
将光标定位到 handler，按下 `alt + enter`，选择 `Create function 'handler'`，则变
为：
```go
func main() {
	http.HandleFunc("/", handler)
}

func handler(writer http.ResponseWriter, request *http.Request) {

}
```

**调试 debug**

???

`ctrl + F8` 设置或取消断点

`alt + F8` 调用计算表达式

`alt + F9` Run to cursor

`F7` 进入调用的函数内部

`ctrl + F2` 停止调试

**其他**

`ctrl + shift + enter` 无需光标移动到行尾，可直接切换到下一行输入

`F2` 移动到高亮的下一个错误点

`ctrl + shift + F10` 编译并运行当前程序，???需要在 main 包中???

