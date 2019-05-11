---
title: Python - Data Types - Tuple
categories:
- Python
tags:
- Python
- Python Tuple
date: 2019-05-06 10:20:47
---

Python - Data Types - Tuple

<!--more-->

## Tuple 和 List

**Tuple 和 List 的共性**
* 都是有序序列
* 都支持索引和重复项
* 都可以存储不同类型数据
* 都支持嵌套

**Tuple 不同于 List 的地方**
* List 是可变的，Tuple 是不可变的，在创建 Tuple 之后不允许增加、删除元素。

**为什么有了 List 还需要 Tuple 数据类型**
* Python 使用 Tuple 类型来从一个函数返回多个值
* Tuple 比 List 轻量
* Tuple 就像一个容器，可以装很多东西
* 可以使用 Tuple 的元素作为字典的键

## 创建 Tuple

Tuples 也是各种数据类型项的混合集合，值可以重复。

创建 Tuple 使用小括号，甚至小括号也可以省略，直接使用逗号分隔元素：
```python
>>> t = (1, 2, 3, 4)
>>> type(t)
<class 'tuple'>

>>> t = 1, 2, 3, 4
>>> type(t)
<class 'tuple'>
```

还可以使用内置函数 tuple 创建，其参数为可迭代对象：
```python
>>> t = tuple("asdf")
>>> t
('a', 's', 'd', 'f')
>>> t = tuple([1, 2, 3, 4])
>>> t
(1, 2, 3, 4)
>>> t = tuple({1, 2, 3, 4})
>>> t
(1, 2, 3, 4)
```

### 常见应用

创建只有一个元素的 Tuple，可以使用 tuple 函数，若使用小括号语法，则元素后面必须
多一个逗号，否则会被认为是其他基本数据类型：
```python
>>> t = (12)
>>> type(t)
<class 'int'>
>>> t = (12,)
>>> type(t)
<class 'tuple'>

>>> t = ('a')
>>> type(t)
<class 'str'>
>>> t = ('a',)
>>> type(t)
<class 'tuple'>


>>> t = tuple(12)
TypeError: 'int' object is not iterable
>>> t = tuple([12])     # 使用 tuple 函数需通过 List 转换
>>> type(t)
<class 'tuple'>
```

空 Tuple：
```python
>>> empty_tuple = ()
>>> type(empty_tuple)
<class 'tuple'>
>>> print(empty_tuple)
()
```

嵌套 Tuple：
```python
>>> first_tuple = (0, 1, 2)
>>> second_tuple = ('a', 'b', 'c')
>>> my_tuple = (first_tuple, second_tuple)
>>> print(my_tuple)
((0, 1, 2), ('a', 'b', 'c'))
```

Tuple 值可以重复：
```python
>>> my_tuple = ('a', 'b', 'c') * 2
>>> print(my_tuple)
('a', 'b', 'c', 'a', 'b', 'c')
```

## 访问 Tuple 元素

### 通过索引

需要一个元素时使用，可使用正数或负数，但都必须是整数，其他类型的索引或导致
TypeError。

正数索引从 0 开始，负数索引尾部取值，从 -1 开始。

### 通过切片

需要获取多个元素时，可通过切片。

```python
>>> my_tuple = (0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
>>> my_tuple[2:5]
(2, 3, 4)
>>> my_tuple[2:]
(2, 3, 4, 5, 6, 7, 8, 9)
>>> my_tuple[:5]
(0, 1, 2, 3, 4)
>>> my_tuple[-4:-2]
(6, 7)
>>> my_tuple[-4:]
(6, 7, 8, 9)
>>> my_tuple[:-4]
(0, 1, 2, 3, 4, 5)
>>> my_tuple[:]
(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
```

## 修改 Tuple

Tuple 是不可变的，在创建 Tuple 之后不允许增加、删除元素。

Tuple 创建后不可以更改：
```python
>>> t = (4, 5, 6)
>>> t[0]
4
>>> t[0] = 3
TypeError: 'tuple' object does not support item assignment
```

但若 Tuple 元素本身是可变的，则可更改其元素：
```python
>>> my_tuple = (0, 1, 2, ['a', 'b', 'c'], 3, 4)
>>> print(my_tuple)
(0, 1, 2, ['a', 'b', 'c'], 3, 4)

>>> my_tuple[3][0] = 'A'
>>> print(my_tuple)
(0, 1, 2, ['A', 'b', 'c'], 3, 4)
```

Tuple 变量也可被直接赋予新的 Tuple 对象：
```python
>>> t
(1, 2, 3)
>>> t = (4, 5, 6)
>>> t
(4, 5, 6)
```

## 移除 Tuple

不能从 Tuple 中删除元素，但可以直接删除整个 Tuple：
```python
>>> b = (4, 5, 6)

>>> del b[0]
TypeError: 'tuple' object doesnot support item deletion

>>> del b
>>> b
NameError: name 'b' is not defined
```

## 其他 Tuple 操作

`+` 可用于扩展元组，把多个元组合并为一个元组，`*` 可用于重复元组中元素指定的次数
：
```python
>>> a = (1, 2, 3)
>>> b = (4, 5, 6)

>>> a + b
(1, 2, 3, 4, 5, 6)

>>> a * 3
(1, 2, 3, 1, 2, 3, 1, 2, 3)

>>> a * 3 + b
(1, 2, 3, 1, 2, 3, 1, 2, 3, 4, 5, 6)
```

in 判断元素是否在元组中：
```python
>>> 'a' in ('a', 'b')
True
>>> 'd' not in ('a', 'b')
True
```

for 循环元组：
```python
>>> for s in (1, 2 ,3):
...     s
...
1
2
3
```

## Tuple 应用举例

### Tuple Assignment

Tuple 支持一个非常直观的特性，称为元组赋值，形式为赋值语句左、右侧都是一个由变量
组成的元组，右侧的元组元素按顺序一一对应对左侧元组元素赋值：
```python
>>> t = (1, 2, 3)
>>> (a, b, c) = t
>>> a
1
>>> b
2
>>> c
3
>>> t = (1, 2, 3, 4, 5, 6)
>>> a, b, c = t[3:6]
>>> a
4
>>> b
5
>>> c
6
```

左右侧元素数量不同会报错：
```python
>>> t = (1, 2, 3, 4, 5, 6)
>>> (a, b, c) = t[4:]
ValueError: not enough values to unpack (expected 3, got 2)

>>> (a, b, c) = t[1:5]
ValueError: too many values to unpack (expected 3)
```

### 元组作为函数返回值

通常，函数只能返回一个值，但可以通过把多个值放入一个元组，从而实现返回多个值：
```python
>>> def getsth():
...     return ('a', 'b', 'c')
...
>>> x, y, z = getsth()
>>> x
'a'
>>> y
'b'
>>> z
'c'
```

### 存储混合数据结构

元组是一种容器类型，它可以同时包含字符串、元组、列表、集合、字典等数据类型。
```python
>>> t = ('a', 123, ('sohu', 'baidu'), ['aa', 'bb', 'cc'], {'A', 'B', 'C'}, {'s':1, 't':2, 'v':3})
>>> type(t)
<class 'tuple'>
```

基于这点儿，它可以帮助我们在更广泛的层次上可视化信息。例如，如果我们必须维护每个
部门的员工数量以及他们的姓名、职位和工资，嵌套元组可以让我们高效地完成这项工作。
```python
employes = [
   ("HR", 2, [('david', 'manager', 100000), ('bruno', 'asst. manager', 50000)])
   ("IT", 2, [('kirk', 'team lead', 150000), ('matt', 'engineer', 45000)])
   ("Sales", 2, [('billy', 'sales lead', 250000), ('tom', 'executive', 95000)])
   ]
```
