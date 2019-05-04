---
title: Python - Data Types - Numbers
categories:
- Python
tags:
- Python
- Python Numbers
date: 2019-05-04 09:37:48
---

Python - Data Types - Numbers

<!--more-->

Python 数字由四种数据类型组成:普通整数、长整数、浮点数和复数。它们不仅支持简单的
算术计算，而且可以作为复数用于量子计算：

Python 2.x : int, long, float, complex
Python 3.x : int, float, complex
Python 3.0 移除了 long 类型，并把 int 类型扩展为无限大小，仅由内存限制。

与 Python 中的其他类型一样，数字也是对象。它们可以存储整数、实数或复数。Python
数字是不可变的对象，因此值的任何更改都会导致创建一个新对象。通常，为变量分配一个
数值会创建 number 对象：
```python
>>> i = 3 + 9j
>>> type(i)
<class 'complex'>
>>> id(i)
2204316616592

>>> i = i + 1
>>> type(i)
<class 'complex'>
>>> id(i)
2204318696080
```

## int

Python 2 中的 int 大小限制：
```python
>>> import sys
>>> sys.maxint
2147483647      # 4 个字节的有符号整数

>>> type(2147483647)
<type 'int'>
>>> type(2147483648)
<type 'long'>
>>> type(-2147483648)
<type 'int'>
>>> type(-2147483649)
<type 'long'>
```

从 Python 3.0 开始， 移除了 long 类型，int 则没有大小限制，其只受可用内存的限制：
```python
>>> num = 798234792641618274123719283712301928301928301928309128312093234324234
>>> num.bit_length()
229

>>> type(798234792641618274123719283712301928301928301928309128312093234324234)
<class 'int'>
```

## long

Python 3.0 移除了 long 类型，并把 int 类型扩展为无限大小，仅由内存限制。

下列是 Python2.x 版本：
```python
>>> type(2147483647)
<type 'int'>
>>> type(2147483648)
<type 'long'>

>>> type(-2147483648)
<type 'int'>
>>> type(-2147483649)
<type 'long'>
```

## float

Python 中 float 的精度可以到 15 位小数：
```python
>>> import sys

>>> sys.float_info
sys.float_info(max=1.7976931348623157e+308, max_exp=1024, max_10_exp=308, min=2.2250738585072014e-308, min_exp=-1021, min_10_exp=-307, dig=15, mant_dig=53, epsilon=2.220446049250313e-16, radix=2, rounds=1)

>>> sys.float_info.dig      # 最大小数位数
15
```

??? 小数位数可以超过 15 位
```python
>>> f = 0.12345678901234567890123128723479823479234928349234
>>> type(f)
<class 'float'>
```

??? 小数比较时有效位到 17 位
```python
>>> f = 0.12345678901234567
>>> g = 0.12345678901234568
>>> print(g > f)
True

>>> f = 0.123456789012345678
>>> g = 0.123456789012345679
>>> print(g > f)
False
```

## complex

除了常见的 int 和 float，Python 还引入了另一种数字类型：complex，这种数据类型由
实数部分和虚数部分组成，例如 `n1 + n2j` 的 n1 和 n2 分别表示复数的实数部分和虚数
部分。
```python
>>> x = 3 + 4j
>>> type(x)
<class 'complex'>
>>> x.real
3.0
>>> x.imag
4.0
```

在数字后添加字母 `j` 或 `J`，可以使它成为复数：
```python
>>> type(8)
<class 'int'>
>>> type(8j)
<class 'complex'>
>>> type(8J)
<class 'complex'>
>>> type(8.1J)
<class 'complex'>
```

使用内置函数 `type()` 确定值或变量的数据类型，使用内置函数 `isinstance()` 测试对
象类型：
```python
>>> i = 10
>>> type(i)
<class 'int'>
>>> isinstance(i, int)
True

>>> f = 10.0
>>> type(f)
<class 'float'>
>>> isinstance(f, float)
True

>>> m = 10j
>>> type(m)
<class 'complex'>
>>> isinstance(m, complex)
True
```

可以使用 complex 类型作为构造函数来形成一个复数：
```python
>>> complex(3, 5)
(3+5j)

>>> type(complex(3, 5))
<class 'complex'>
```

## Key Points

1. 数字类型按下列顺序自动向上转换：
    int     -      lng     -      float       -      complex

2. Python 3.x 中的 int 类型可以是任意长度的，float 类型只能精确到 15 位小数。

3. 通常我们使用 10 进制，但有时需要使用 2 进制、8 进制、16 进制，Python 中通过使
   用数字前缀来表示其他进制：
   Binary   0b 或 0B
   Octal    0o 或 0O
   Hex      0x 或 0X
   ```python
    >>> print(0b110)
    6
    >>> print(0xFF)
    255

    >>> type(0o123)
    <class 'int'>
    >>> print(0o123)
    83
   ```

4. 使用 `isinstance(object, class)` 来测试数字所属的类
   ```python
   >>> isinstance(2.2, float)
   True
   ```

5. 如果表达式中混合使用数字数据类型，则所有的操作数都将自动转为最复杂的数据类型

6. 对 int 使用除法时注意，Python 2.x 中的 int 相除返回整数 int 作为商值，而
   Python 3.x 则返回浮点数作为商值：
   ```python
   # python 2.x
   >>> 7/2
   3
   >>> type(7/2)
   <type 'int'>

   # python 3.x
   >>> 7/2
   3.5
   >>> type(7/2)
   <class 'float'>
   ```

7. 整除运算符 `//` 返回 int 型商，取余运算符返回 float 型余数：
   ```python
   >>> 7//2
   3
   >>> 7%2
   1
   >>> divmod(7, 2)
   (3, 1)
   ```

## Type Conversion

基本操作中，类型会进行隐式转换：
```python
>>> 2 + 5.0
7.0
>>> type(2 + 5.0)
<class 'float'>
```

除此之外，Python 还提供了显式转换的内置函数：int(), float(), complex()，除了可以
抓换数字，还可以转换字符串：
```python
>>> int(2 + 5.0)
7
>>> float(2)
2.0
>>> complex(23)
(23+0j)

>>> int("123")
123
>>> float("123")
123.0
>>> complex("123")
(123+0j)
```

## External Classes To Handle Python Numbers

Python 的内置 float 类有一个限制，即控制精度不能超过十五位小数。但是还有其他限制
，例如下面的小数点问题，这都是因为它完全依赖于浮点数的计算机实现：
```python
>>> 1.1 + 3.2
4.300000000000001
```

我们可以使用 Python 的 decimal、fractions 模块来处理上述问题。

### decimal

The decimal module provides the fixed and floating point arithmetic
implementation which is familiar to most people. Unlike the floating point
numbers which have precision up to 15 decimal places, the decimal module accepts
a user-defined value. It can even preserve significant digits in a no.

decimal 模块提供了定点数和浮点数的运算实现，不像 float 类型有 15 位小数的精度限
制，decimal 模块接收用户输入的值，它还可以保留有效数字：
```python
>>> import decimal
>>> print(0.28)
0.28
>>> print(decimal.Decimal(0.28))
0.2800000000000000266453525910037569701671600341796875
>>> print(decimal.Decimal('0.28'))
0.28
```

### fractions

fractions 模块用于处理分数计算。

分数由分子和分母组成，两者都是整数数据类型。此模块支持有理数算术功能。

```python
import fractions
>>> print(fractions.Fraction(2.5))
5/2
>>> print(fractions.Fraction(5.2))
5854679515581645/1125899906842624
>>> print(fractions.Fraction(3, 5))
3/5
>>> print(fractions.Fraction(1.3))
5854679515581645/4503599627370496
>>> print(fractions.Fraction('3.7'))
37/10
```

## Mathematics 

Python 公开了一些内置函数来执行简单的数学计算，例如：

`abs(), cmp(), max(), min(), round()`

除了内置函数，还可以使用 math 模块，其提供的常用数学方法如下：

**ceil**

向正无穷方向取整
```python
import math
>>> math.ceil(1.2)
2
>>> math.ceil(1.7)
2
>>> math.ceil(-1.2)
-1
>>> math.ceil(-1.7)
-1
```

**floor**

向负无穷方向取整
```python
>>> from math import floor
>>> floor(1.2)
1
>>> floor(1.7)
1
>>> floor(-1.2)
-2
>>> floor(-1.7)
-2
```

**pi and e**

数学常数 pi 和 e：
```python
>>> import math
>>> math.pi
3.141592653589793
>>> math.e
2.718281828459045
```

max(x1, x2,…)	The largest of its arguments: the value closest to positive infinity
min(x1, x2,…)	The smallest of its arguments: the value closest to negative infinity
cmp(a, b)	-1 if a < b, 0 if a == b, or 1 if a > b

abs(x)	The absolute value of x: the (positive) distance between x and zero.
exp(x)	The exponential of x: ex
log(x)	The natural logarithm of x, for x> 0
log10(x)	The base-10 logarithm of x for x> 0.
pow(x, y)	The value of x**y
sqrt(x)	The square root of x for x > 0

modf(x)	The fractional and integer parts of x in a two-item tuple. Both parts share the same sign as x. The integer part coerces into a float.
round(x [,n])	x rounded to n digits from the decimal point.
