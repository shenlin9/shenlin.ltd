---
title: Python - Keywords, Identifiers and Variables
categories:
- Python
tags:
- Python
- Keywords
- Identifiers
date: 2019-04-29 14:03:37
---

Python - Keywords, Identifiers and Variables

<!--more-->

## Keywords

### 获取关键字列表

进入 Python Interpreter，帮助下获取关键字列表
```python
>>> help()

help> keywords

Here is a list of the Python keywords.  Enter any keyword to get more help.

False               class               from                or
None                continue            global              pass
True                def                 if                  raise
and                 del                 import              return
as                  elif                in                  try
assert              else                is                  while
async               except              lambda              with
await               finally             nonlocal            yield
break               for                 not
```

另一种方法使用 Python 的 keyword 模块获取关键字列表，注意提示符，同前一个命令的
执行环境不同：
```python
>>> import keyword
>>> keyword.kwlist
['False', 'None', 'True', 'and', 'as', 'assert', 'async', 'await', 'break',
'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for',
'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or',
'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```

kwlist 是一个数组，不是方法因此不要使用括号调用：
```python
>>> keyword.kwlist()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'list' object is not callable
```

### Keywords

Python 2.7 共 31 个关键字：

    and                 elif                if                  print
    as                  else                import              raise
    assert              except              in                  return
    break               exec                is                  try
    class               finally             lambda              while
    continue            for                 not                 with
    def                 from                or                  yield
    del                 global              pass

Python 3.7.2 共 35 个关键字：

    False               class               from                or
    None                continue            global              pass
    True                def                 if                  raise
    and                 del                 import              return
    as                  elif                in                  try
    assert              else                is                  while
    async               except              lambda              with
    await               finally             nonlocal            yield
    break               for                 not

Python 3.7 多出来 6 个：

    None                await               async               nonlocal

    False               True                

同时少了 2 个，都由语句变为了内置函数：

    exec                return  

关键字是一个编程术语，是一些儿具有特定含义的特殊词，旨在执行某些操作，它们表示了
程序的语法和结构。

关键字作为保留字，不能被用作变量名、类名、方法名。

Python 的关键字区分大小写。

## Identifiers

标识符是由用户定义用于表示变量，函数，类，模块或任何其他对象的名称。 

Python 中创建标识符的规则：
* 标识符由大小写字母、数字和下划线组成 `[a-zA-Z0-9_]`
* 标识符不能以数字开头，否则语法错误
* 关键字是保留字，不能用作标识符
* 不能包含特殊字符  `[‘.’, ‘!’, ‘@’, ‘#’, ‘$’, ‘%’]`
* 原则上不限制标识符的长度，但实际上违反 PEP-8 标准:"Limit all lines to a
  maximum of 79 characters"

测试某个标识符是否是系统保留的关键字，可以使用 keyword 模块的 iskeyword 方法，注
意参数为字符串类型：
```python
>>> import keyword
>>> keyword.iskeyword('funcname')
False
>>> keyword.iskeyword('If')
False
>>> keyword.iskeyword('if')
True
```

测试某个标识符是否合法，可以使用字符串的 `str.isidentifier()` 方法：
```python
>>> 'func_name'.isidentifier()
True
>>> 'func.name'.isidentifier()
False
```

标识符命名最佳实践：
* 类名最好以大写字母开头，所有其他标识符以小写字母开头
* 私有标识符以下划线开头 ???和下面一条冲突
* 不要用下划线在标识符的开始和结尾，Python 的内置类型已经使用了这种方法
* 使用有含义的名字，如 index 或 iter 比 i 更有含义
* 使用下划线连接单词来组成有含义的名字

## Variables

为内存中存储数据的位置分配一个标签，以便我们更方便的读写其数据，这个标签就是变量
，内存中存储数据的位置逻辑上就是一个实体。

有关 Python 变量的一些关键事实：
* 变量不需要声明，但在使用之前必须初始化
```python
>>> print(s)    # 直接使用出错 not defined
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 's' is not defined

>>> s = 10      # 先初始化再使用
>>> print(s)
10
```
* `s = 10` 表达式导致下列操作：
  * 创建一个表示值 10 的 int 对象
  * 如果变量 s 不存在，则创建它
  * 关联变量 s 到 int 对象，则可通过变量来读写 int 对象，所以变量 s 是对值 10 的
      引用
* 每当表达式发生变化时，Python都会将一个新对象（一块内存）与该变量相关联，以便引
用新值。 旧值则进入垃圾回收器。
```python
>>> test = 10
>>> id(test)
140728158643312

>>> test = 11
>>> id(test)
140728158643344

>>> test = 10
>>> id(test)
140728158643312
```
* 为了优化，Python 构建了一个缓存用以重复使用一些不可变对象，例如小整数和字符串。
* 一个对象是一块内存区域，可以存储下列内容
  * 真实的对象值
  * 反映对象类型的类型指示符
  * 用于确定何时可以回收对象的引用计数器
* 拥有类型的是对象，不是变量，但变量可以在需要时引用不同类型的对象，例如：
    ```python
    >>> test = 123
    >>> type(test)
    <class 'int'>

    >>> test = 123.3
    >>> type(test)
    <class 'float'>

    >>> test = "123.3"
    >>> type(test)
    <class 'str'>
    ```

## Others

An object is a region of memory that has a type. 
对象是内存中具有类型的一块区域。



