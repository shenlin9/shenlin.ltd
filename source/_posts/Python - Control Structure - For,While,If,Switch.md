---
title: Python - Control Structure - For,While,If,Switch
categories:
- Python
tags:
- Python
- For
- Range
- While
- If
- Switch
date: 2019-05-12 17:39:15
---

Python - Control Structure - For,While,If,Switch

Python 支持七种序列数据类型：
* standard/Unicode strings
* a list
* tuples
* a bytearray
* xrange
* 集合，但只是序列类型的容器
* 字典，但只是序列类型的容器

<!--more-->

## Range

range 在运行时生成一个整数序列:
```python
>>> type(range(3))
<class 'range'>

>>> range(3)[2]
2
>>> range(3)[3]
IndexError: range object index out of range

>>> for i in range(4):
...     i
...
0
1
2
3

>>> for i in range(3, 7):
...     i
...
3
4
5
6

>>> for i in range(2,9,2):
...     i
...
2
4
6
8
```

## For ... In

for 循环是 Python 程序中最常用的控制流语句。

```python
for iter in sequence:
    statement(iter)
```

通常 for 循环是取回序列中的元素赋值给迭代变量，但也可以结合 range 使用来取回索引：
```python
>>> ls = ['a','b','c']

>>> for s in ls:
...     s
...
'a'
'b'
'c'

>>> for s in range(len(ls)):
...     print(s,'--',ls[s])
...
0 -- a
1 -- b
2 -- c
```

### For ... Else

Python 的 for 循环还支持一个可选的 else 子句：
```python
for item in seq:
    statement 1
    statement 2
    if <cond>:
        break
else:
    statements
```

当 for 正常循环结束则执行 else 语句，若使用 break 跳出循环则不执行 else 语句：
```python
>>> for s in ['a','b','c']:
...     s
... else:
...     print('finished success')
...
'a'
'b'
'c'
finished success

>>> for s in ['a','b','c']:
...     s
...     if s is 'b':
...             break
... else:
...     print('finished success')
...
'a'
'b'
```

## While

for 循环执行到某个次数完成循环，而 while 循环依赖于一个条件来完成执行。

在编码时，可能会出现不知道循环截止点的情况。例如，一个程序需要不确定的用户输入次
数，直到他按下 ESC 键或读取一个文件直到它找到一个特定的符号。

也被称为预测试循环，因为它在每次迭代之前都会检查条件。

```python
while some condition (or expression) :
    a block of code
```

Python 的 while 循环也支持一个可选的 else 子句：
```python
while some condition (or expression) :
    statement 1
    statement 2
    if <cond>:
        break
else:
    statements
```

```python
>>> i = 0
>>> while i < 5:
...     i
...     i += 1
... else:
...     'finished'
...
0
1
2
3
4
'finished'
```

## IF

```python
if Logical_Expression :
    Indented Code Block


if Logical_Expression :
    Indented Code Block 1
else :
    Indented Code Block 2


if Logical_Expression_1 :
    Indented Code Block 1
elif Logical_Expression_2 :
    Indented Code Block 2
elif Logical_Expression_3 :
    Indented Code Block 3
...
else :
    Indented Code Block N
```

### Not、And、In Operator

not
```python
>>> if not 10 > 20:
...     print('10 <= 20')
...
10 <= 20

>>> x = 0
>>> if not x:
...     print(x)
...
0
```

in
```python
>>> if 'a' in ('a','b','c'):
...     print('yes')
...
yes
```

## Switch

Switch case 是模块化编程中常用的一种功能强大的决策构造。

如果您不想让一个条件块混杂着多个 if 条件，那么 switch case 可以为在程序中实现控
制流提供一种更简洁的方法。

但 Python 没有提供此语法结构，可以用多种方法自己实现 Python 的 switch case 语句。

使用字典实现 Switch case 语句：
```python
def monday():
    return "monday"
def tuesday():
    return "tuesday"
def wednesday():
    return "wednesday"
def thursday():
    return "thursday"
def friday():
    return "friday"
def saturday():
    return "saturday"
def sunday():
    return "sunday"
def default():
    return "Incorrect day"

switcher = {
    1: monday,
    2: tuesday,
    3: wednesday,
    4: thursday,
    5: friday,
    6: saturday,
    7: sunday
    }

def switch(dayOfWeek):
    return switcher.get(dayOfWeek, default)()

print(switch(1))
print(switch(0))
```

使用类实现 Switch：
```python
class PythonSwitch:

    def switch(self, dayOfWeek):
        default = "Incorrect day"
        return getattr(self, 'case_' + str(dayOfWeek), lambda: default)()

    def case_1(self):
        return "Monday"
 
    def case_2(self):
        return "Tuesday"
 
    def case_3(self):
        return "Wednesday"

s = PythonSwitch()

print(s.switch(1))
print(s.switch(0))
```

关于 Python 为什么没有提供 Switch case
参考 https://www.python.org/dev/peps/pep-3103/

