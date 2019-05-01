---
title: Python - Statement, Expression and Indentation
categories:
- Python
tags:
- Python
date: 2019-04-30 09:45:26
---

Python - Statement, Expression and Indentation

<!--more-->

## Python Statements

Python 中的语句是 Python 解释器可以读取和执行的逻辑指令。 在 Python 中，它可以是
表达式或赋值语句。

赋值语句是 Python 的基础。 它定义了表达式创建对象并保留对象的方式。

表达式是 Python 语句的一种类型，它包含数字，字符串，对象和运算符的逻辑序列。 值
本身是一个有效的表达式，变量也是。

### Simple Assignment Statement

Python 中有 3 种类型的赋值语句：
* Case-1: Right-Hand Side (RHS) Is Just A Value-Based Expression.
* Case-2: Right-Hand Side (RHS) Is A Current Python Variable.
* Case-3: Right-Hand Side (RHS) Is An Operation.

#### Case-1: Right-Hand Side (RHS) Is Just A Value-Based Expression.

例如
```python
test = "Learn Python"
```

通过内置函数 `id()` 可以获取其内存地址：
```python
>>> id(test)
2059613406896
```

如果相同的字符串内容创建变量，Python 将创建一个新对象并将为其分配一个新内存地址
。 这条规则适用于大多数情况。
```python
>>> test1 = "Learn Python"
>>> id(test1)
2059613406704

>>> test2 = "Learn Python"
>>> id(test2)
2059619329200
```

但在下面两种情况下，Python 将分配同样的内存地址：
* 字符串不包含空白且少于 20 个字符
* 整数值且其范围 -5 到 +255
这个概念被称为 Interning，用于节省内存。
```python
>>> d = 12
>>> id(d)
140718137271472
>>> k = 12
>>> id(k)
140718137271472

>>> s = "abcdef"
>>> id(s)
2059614000328
>>> ss = "abcdef"
>>> id(ss)
2059614000328
```

#### Case-2: Right-Hand Side (RHS) Is A Current Python Variable.

例如
```python
test = "Learn Python"
test2 = test
```
则上面的第二条语句不会触发新内存分配，两个变量都指向同一个内存地址，像是为已存在
的对象创建了一个别名，验证：
```python
>>> test = "Learn Python"
>>> test2 = test
>>> id(test)
2453548008304
>>> id(test2)
2453548008304
```

#### Case-3: Right-Hand Side (RHS) Is An Operation.

这种类型的语句中，结果取决于操作的结果。

如下面的例子中，右侧的操作分别导致创建整型和浮点型变量：
```python
>>> test = 20 / 2
>>> type(test)
<class 'float'>

>>> test = 5 * 2
>>> type(test)
<class 'int'>
```

### Augmented Assignment Statement

可以在赋值中组合算术运算符以形成增强赋值语句。

```python
>>> my_tuple = (10, 20, 30)
>>> my_tuple += (40, 50)
>>> print(my_tuple)
(10, 20, 30, 40, 50)

>>> list_vowels = ['a', 'e', 'i']
>>> list_vowels += ['o', 'u']
>>> print(list_vowels)
['a', 'e', 'i', 'o', 'u']
```

## Python Multi-line Statement

有两种方式在 Python 程序中使用跨行语句

### Explicit line continuation

显式的使用跨行符 `\`
```python
>>> list = ['a', 'e',\
... 'i', 'o',\
... 'u']

>>> print(list)
['a', 'e', 'i', 'o', 'u']
```

### Implicit line continuation

隐式的跨行指的是当使用小括号、中括号、大括号时，都必须配对闭合，则括号内的内容在
闭合前都是可以跨行的：
```python
>>> result = (5 + 10
... - 5
... * 2
... )
>>> print(result)
5

>>> fruits = [
...     "apple",
...     "banana",
...     "pear",
... ]
>>> print(fruits)
['apple', 'banana', 'pear']
```

注意这种方式字符串不能被截断：
```python
>>> print("asdf"
... + "jjjj"
... + "kkk"
... )
asdfjjjjkkk

>>> print("asdf
  File "<stdin>", line 1
    print("asdf
              ^
SyntaxError: EOL while scanning string literal

>>> print("asdf\    # 第一种方式可以截断字符串
... jjjj\
... kkkk\
... llll"\
... )
asdfjjjjkkkkllll
```

## Indentation

Python 使用缩进来标记代码块，用于表示函数体或循环的代码块以缩进开始，以第一个未
缩进行结束。

PEP8 规定以 4 个空格来进行缩进，但 Google 使用的是 2 个空格，推荐使用 PEP8 的格
式。
