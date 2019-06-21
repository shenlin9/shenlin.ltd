---
title: Python - Tips and Tricks
categories:
- Python
tags:
- Python
- Tips
- Tricks
date: 2019-06-04 15:59:40
---

Python - Tips and Tricks

<!--more-->

## 就地交换值 

```python
>>> x, y = 1, 2
>>> x, y
(1, 2)

>>> x,y = y,x
>>> x,y
(2, 1)
```

## 比较运算符的链接

```python
>>> r = 1 < 3 < 5
>>> r
True

>>> r = 3 < 2 < 5
>>> r
False
```

## 使用三元运算符进行条件赋值

```python
[on_true] if [expression] else [on_false]
```

```python
>>> x = 10 if 1 < 2 else 20
>>> x
10

>>> x = 10 if 3 < 2 else 20
>>> x
20
```

还可用于类对象：
```python
x = (classA if y == 1 else classB)(param1, param2)
```

## 条件表达式

```python
>>> count = 4
>>> s = 'odd' if count % 2 else 'even'
>>> s
'even'
```

```python
>>> data = 'abc'
>>> s = data.upper() if isinstance(data, str)  else 'not string'
>>> s
'ABC'

>>> data = 123
>>> s = data.upper() if isinstance(data, str)  else 'not string'
>>> s
'not string'
```

## 使用 Enumerate() 函数

此函数的作用是:将计数器添加到可迭代对象中。

```python
>>> for s in ('a','b','c'):
...     s
...
'a'
'b'
'c'

>>> for k, s in enumerate(('a','b','c')):
...     k,s
...
(0, 'a')
(1, 'b')
(2, 'c')
```

## 赋值和比较

赋值用 `=` 比较是否相等用 `==`

Python 不支持行内赋值(inline)，所以不会发生想要比较时却意外赋值的情况。

## 模块

Python Module
```python
pip install w3
pip3 install w3
```

## Module

```python
pip3 install numpy
pip3 install os
pip3 install like
pip3 install ex
pip3 install system

def fn():

    install os
    install system
    install numpy

def fn():

    s = [1, 2, 3, 4]
    for i in s:
        i

    for i,k in numerate(s):
        i,k

def test():
    print(s.format())
```

## 条件表达式

```python
if a < b:
    print('b')
elif a = b:
    print('a')
else:
    print('d')
```

## 使用元组、列表、集合、字典初始化变量

注意元素数量和变量数量要一样：
```python
>>> x,y,z = ['a','b','c','d']
ValueError: too many values to unpack (expected 3)

>>> x,y,z = ['a','b']
ValueError: not enough values to unpack (expected 3, got 2)

>>> x,y,z = ['a','b','c']
>>> x,y,z
('a', 'b', 'c')
```

元组也可以
```python
>>> x,y,z = ('a','b','c')
>>> x,y,z
('a', 'b', 'c')
```

集合也可以，但集合是无序的，所以和变量的对应关系会不确定：
```python
>>> x,y,z = {'a','b','c'}
>>> x,y,z
('c', 'b', 'a')
```

字典则是用键来为变量赋值：
```python
>>> x,y,z = {'a':1,'b':2,'c':3}
>>> x,y,z
('a', 'b', 'c')
```

