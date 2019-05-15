---
title: Python - Namespace
categories:
- Python
tags:
- Python
- Python Namespace
date: 2019-05-12 13:56:16
---

Python - Namespace

<!--more-->

## 命名机制

命名机制与 Python 的对象系统内联工作，即 Python 中的所有东西都是一个对象。所有的
数据类型，如数字、字符串、函数、类都是对象。而名称则充当获取对象的引用。

## 名称空间

名称空间是定义范围的一种实用方法，它有助于避免名称冲突。在 Python 中，名称空间是
构造和组织代码的基本思想，在大型项目中尤其有用。

名称空间是一个简单的系统，用于控制程序中的名称。它确保名称是惟一的，不会导致任何
冲突。

Python 以字典的形式实现名称空间。它维护一个名称到对象的映射，其中名称充当键，对
象充当值。多个名称空间中可能具有相同的名称，但指向不同的变量。

### Local Namespace

这个命名空间包含函数中的本地名称。

Python为程序中调用的每个函数创建这个名称空间。

在函数返回之前，它一直保持活动状态。

### Global Namespace

这个名称空间包含项目中各种被导入的模块的名称。

Python 为程序中包含的每个模块创建这个命名空间。它将持续到程序结束。

### Built-in Namespace

这个命名空间包含内置函数和内置异常名称。

Python 在解释器启动时创建它，并保持它直到您退出。

## Scope

作用域

名称空间使我们的程序免受名称冲突的影响。然而，它并不能让我们在任何地方自由的使用
变量名。

Python 通过特定规则来限制待绑定的名称(names to be bound)，这个规则就是作用域。作
用域确定了程序中哪些部分可以不使用任何前缀而直接使用该名称。

* Python 为局部变量、函数、模块和内置描述了不同的作用域。
* 局部作用域(也称为最内层作用域)包含当前函数中可用的所有局部名称的列表。
* 所有封闭函数的作用域，它从最近的封闭作用域找到一个名称并向外扩展。
* 模块级作用域，它负责处理当前模块中的所有全局名称。
* 管理所有内置名称列表的最外层作用域。它是搜索程序中引用的名称的最后一个地方。

## Scope Resolution 

对于一个给定名称的解析，先从最里层的函数开始，然后越来越外层，直到程序找到相关的
对象，如果搜索结束却没有找到，则程序抛出 NameError 异常。

可以使用 `dir([object])` 函数查看作用域的名称，当没有参数时返回当前作用域的所有
名称，当传递对象参数后，将显式对象下的名称：
```python
>>> dir()
['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__']

>>> dir(math)
NameError: name 'math' is not defined

>>> import math
>>> dir(math)
['__doc__', '__loader__', '__name__', '__package__', '__spec__', 'acos', 'acosh', 'asin', 'asinh', 'atan', 'atan2', 'atanh', 'ceil', 'copysign', 'cos', 'cosh', 'degrees', 'e', 'erf', 'erfc', 'exp', 'expm1', 'fabs', 'factorial', 'floor', 'fmod', 'frexp'
, 'fsum', 'gamma', 'gcd', 'hypot', 'inf', 'isclose', 'isfinite', 'isinf', 'isnan', 'ldexp', 'lgamma', 'log', 'log10', 'log1p', 'log2', 'modf', 'nan', 'pi', 'pow', 'radians', 'remainder', 'sin', 'sinh', 'sqrt', 'tan', 'tanh', 'tau', 'trunc']
>>>
```

作用域举例：
```python
>>> a_var = 10
>>> dir()
['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'a_var']

>>> def foo():
...     b_var = 11
...     print(dir())
...
>>> foo()
['b_var']

>>> dir()
['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'a_var', 'foo']
```
在定义foo()之后调用dir()将其推到全局名称空间中可用的名称列表中。

嵌套作用域举例：
```python
>>> def outer_foo():
...     outer_var = 3
...     def inner_foo():
...         inner_var = 5
...         print(dir(), ' - names in inner_foo')
...     outer_var = 7
...     inner_foo()
...     print(dir(), ' - names in outer_foo')
...
>>> outer_foo()
['inner_var']  - names in inner_foo
['inner_foo', 'outer_var']  - names in outer_foo
```
上面的例子定义了 `outer_foo()` 范围内的两个变量和一个函数。
在`inner_foo()`函数中，`dir()`函数只显示一个名称，即 `inner_var`。这没有问题，因
为 `inner_var` 是惟一在其中定义的变量。

如果在局部名称空间中重复使用了全局名称，那么 Python 将创建一个具有相同名称的新局
部变量：
```python
a_var = 5
b_var = 7

def outer_foo():
    global a_var
    a_var = 3
    b_var = 9
    def inner_foo():
        global a_var
        a_var = 4
        b_var = 8
        print('a_var inside inner_foo :', a_var)
        print('b_var inside inner_foo :', b_var)
    inner_foo()
    print('a_var inside outer_foo :', a_var)
    print('b_var inside outer_foo :', b_var)

outer_foo()
print('a_var outside all functions :', a_var)
print('b_var outside all functions :', b_var)
```

## Import Modules

### 从模块中导入所有名称

```python
from <module name> import *
```

它会直接将模块中的所有名称导入到您的工作命名空间中。 

但是使用这种方式，您可能无法分辨哪个模块导入了特定的函数。

```python
>>> print("namespace_1: ", dir())
namespace_1:  ['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__']

>>> from math import *
>>> print("namespace_2: ", dir())
namespace_2:  ['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'acos', 'acosh', 'asin', 'asinh', 'atan', 'atan2', 'atanh', 'ceil', 'copysign', 'cos', 'cosh', 'degrees', 'e', 'erf', 'erfc', 'exp', 'expm
1', 'fabs', 'factorial', 'floor', 'fmod', 'frexp', 'fsum', 'gamma', 'gcd', 'hypot', 'inf', 'isclose', 'isfinite', 'isinf', 'isnan', 'ldexp', 'lgamma', 'log', 'log10', 'log1p', 'log2', 'modf', 'nan', 'pi', 'pow', 'radians', 'remainder', 'sin', 'sinh', '
sqrt', 'tan', 'tanh', 'tau', 'trunc']
>>> print(sqrt(144.2))
12.00833044182246

>>> from cmath import *
>>> print("namespace_3: ", dir())
namespace_3:  ['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'acos', 'acosh', 'asin', 'asinh', 'atan', 'atan2', 'atanh', 'ceil', 'copysign', 'cos', 'cosh', 'degrees', 'e', 'erf', 'erfc', 'exp', 'expm
1', 'fabs', 'factorial', 'floor', 'fmod', 'frexp', 'fsum', 'gamma', 'gcd', 'hypot', 'inf', 'infj', 'isclose', 'isfinite', 'isinf', 'isnan', 'ldexp', 'lgamma', 'log', 'log10', 'log1p', 'log2', 'modf', 'nan', 'nanj', 'phase', 'pi', 'polar', 'pow', 'radia
ns', 'rect', 'remainder', 'sin', 'sinh', 'sqrt', 'tan', 'tanh', 'tau', 'trunc']
>>> print(sqrt(144.2))
(12.00833044182246+0j)
>>>
```

在本例中，我们导入了两个不同的数学模块，一个接一个。这两个模块都有一些共同的名称
。因此，第二个模块将覆盖第一个模块中的函数定义。

对 `sqrt()` 的第一个调用返回一个实数，第二个调用返回一个复数。因为被覆盖，我们现
在无法从第一个数学模块调用 `sqrt()` 函数。

即使我们使用模块名调用函数，Python 也会引发 NameError 异常。因此，这里学到的教训
是，对于高质量代码没有捷径。

### 从模块中导入指定名称

```python
from <module name> import <foo_1>, <foo_2>
```

如果可以确定要从模块中使用的名称，则直接将它们导入程序。此方法稍微好一些，但不能
完全避免污染名称空间。这是因为您不能使用模块中的任何其他名称。同样，程序中任何具
有相同名称的函数也将覆盖模块中相同的定义。在这种情况下，受影响的方法将处于休眠状
态。

```python
>>> print("namespace_1: ", dir())
namespace_1:  ['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__']

>>> from math import sqrt, pow
>>> print("namespace_2: ", dir())
namespace_2:  ['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'pow', 'sqrt']
>>> print(sqrt(144.2))
12.00833044182246
```

### 直接导入模块

```python
import <module name>
import <module name> as newname
```

这是导入模块的最可靠和建议使用的方法。

但是有一个缺陷，就是需要在使用模块的任何名称之前为名称加上模块前缀。

但这样也可以防止程序污染命名空间，并在模块中使用匹配的名称自由定义函数。

```python
>>> dir()
['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__']

>>> import math
>>> dir()
['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'math']

>>> print(math.sqrt(144.2))
12.00833044182246

>>> import math as m
>>> dir()
['__annotations__', '__builtins__', '__doc__', '__loader__', '__name__', '__package__', '__spec__', 'math', 'm']
```
