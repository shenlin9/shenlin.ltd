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

**光标移动**

`ctrl + shift + enter` 无需光标移动到行尾，可直接切换到下一行输入

`F2` 移动到高亮的下一个错误点

`ctrl + shift + F10` 编译并运行当前程序，???需要在 main 包中???

