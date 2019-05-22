---
title: Python - Lambda
categories:
- Python
tags:
- Python
- Lambda
date: 2019-05-21 07:28:27
---

Python - Lambda

<!--more-->

Python 中创建函数的两种方法：
* 使用 def 关键字，会创建一个函数对象并给它分配一个名称
* 使用 lambda 操作符，会创建一个行内函数并把此函数作为结果返回

## Lambda

Lambda 操作符，又称为匿名函数，属于行内的轻量匿名函数，可以接受任意多个参数，但
只能有一个表达式，它使用表达式的形式来生成函数对象。

语法：
```python
lambda arg1, arg2, ... argN: expression using arguments
```

lambda 函数的主体类似于 def 主体的返回语句，不同之处在于，这里是一个类型表达式，
而不是显式返回它。

请注意 lambda 函数不能包含任何语句。它只返回一个函数对象，您可以将其分配给任何变
量。

lambda 语句可以出现在不允许 def 出现的地方。例如，在列表字面量或函数调用的参数中
，等等：

列表中的 lambda：
```python
>>> ls = [lambda m:m*2, lambda m,n:m*n, lambda m:m**2]
>>> ls[0](2)
4
>>> ls[1](2,3)
6
>>> ls[2](3)
9
```

字典中的 lambda：
```python
>>> d = {'m':lambda x:2*x, 'n':lambda x:x**2}
>>> d['m'](3)
6
>>> d['n'](3)
9
```

## lambda and filter() and map()

一些方法可以接受函数对象作为参数，被称为高阶函数。

可以将 lambda 表达式作为参数传递给高阶函数。

Python 提供了两个内置高阶函数 filter() 和 map()，它们可以接收 lambda 函数作为参数。

通过将 lambda 函数与 filter() 和 map() 函数一起使用来扩展 lambda 函数的效用。

### map()

```python
map(function_object, iterable1, iterable2,...)
```
map() 函数允许我们对一组可迭代对象调用一个函数，第一个参数是函数对象，后面的参数
是可迭代对象，可迭代对象的数量必须和函数对象的参数数量相同，map() 函数会遍历所有
可迭代对象，并对每个可迭代对象的元素调用函数对象：
```python
>>> def test(a):
...     return a.upper()
...
>>> list(map(test, ['a','b','c']))
['A', 'B', 'C']
```

注意参数数量要对应上：
```python
>>> def test(a):
...     return a.upper()
...
>>> def test2(a, b):
...     return a+b
...
>>> list(map(test, ['a','b','c'], ['d', 'e', 'f']))
TypeError: test() takes 1 positional argument but 2 were given
...
>>> list(map(test2, ['a','b','c']))
TypeError: test2() missing 1 required positional argument: 'b'
...
>>> list(map(test2, ['a','b','c'], ['d', 'e', 'f']))
['ad', 'be', 'cf']
```

map 的第一个参数函数对象当然也可以指定为一个 Python lambda 函数：
```python
>>> list(map(lambda x:x.upper(), ('a','b','c')))
['A', 'B', 'C']

>>> for s in map(lambda x:x*2, [1,2,3,4]):
...     s
...
2
4
6
8
```

### filter()

```python
filter(function_object, list)
```
像 map() 函数一样，对可迭代对象的元素依次调用函数对象，但只有函数中评估为 True
的元素才会保留在返回结果里。

```python
>>> alphabeta = ['a','b','c','d','e']
>>> vowels = ['a','e','i','o','u']
>>> list(filter(lambda x: x in vowels, alphabeta))
['a', 'e']
```

### reduce()

```python
reduce(func_obj, iterable[, initializer])
```
reduce 函数可以让我们对序列中的值连续进行滚动计算，用于聚合列表中的数据并返回一
个结果，它会对迭代对象的元素依次调用函数对象，但最终返回的是一个结果值。

```python
>>> def fn(x, y): return x + y
...
>>> from functools import reduce
>>> reduce(fn, [1,2,3,4,5])
15
>>> reduce(lambda x,y:x + y, [1,2,3,4,5])
15
```
