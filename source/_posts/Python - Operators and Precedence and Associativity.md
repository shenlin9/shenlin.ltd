---
title: Python - Operators and Precedence and Associativity
categories:
- Python
tags:
- Python
- Python Operators
date: 2019-05-11 16:10:19
---

Python - Operators and Precedence and Associativity

<!--more-->

## Arithmetic operators

    +
    -
    *
    /       float division
    //      floor division
    %       取余
    **      次方

```python
>>> 9 / 2
4.5
>>> 9 // 2
4
>>> -9 // 2
-5
>>> 9 % 2
1
>>> 9 ** 2
81
```

## Comparison operators

    >
    <
    ==
    !=
    >=
    <=

## Logical operators

    and
    or
    not

not 操作符直接返回布尔值
```python
>>> not 9
False
>>> not 0
True
>>> not -2
False
```

而 and 和 or 操作符返回的是操作数：
a and b 如果 a 等价于 False，则返回 a，否则返回 b
```python
>>> 8 and 0
0
>>> 8 and 1
1
>>> 8 and -1
-1

>>> 0 and 8
0
>>> 1 and 8
8
>>> -1 and 8
8
```

a or  b 如果 a 等价于 False，则返回 b，否则返回 a
```python
>>> 8 or 0
8
>>> 8 or -1
8
>>> 8 or 1
8

>>> 0 or -1
-1
>>> 0 or 1
1
>>> 0 or 8
8
```

## Bitwise operators

    &
    |
    ~   按位取反
    ^   按位异或，任意一位为 1 但不全为 1 时返回 1
    >>  a >> b，把 a 向右移动 b 位
    <<

## Assignment operators

    =
    +=
    -=
    *=
    /=
    %=
    **=

    &=
    |=
    ^=
    >>=
    <<=

    ???没有 ^=

## Advance Operators

### Identity operators

    is
    is not

用于比较两个变量/对象的内存地址，可以让我们确认是否两个变量/对象使用的是同一个内
存地址：
```python
>>> a = 'abcd'
>>> b = 'abc'
>>> c = a

>>> a is b
False
>>> a is c
True
```

???
```python
>>> a = 'abc'
>>> a is 'abc'
True
>>> a is not 'abc'
False
>>> id(a)
2250217740360
>>> id('abc')
2250217740360
```

还可用于确认某个值是否是某个类型或类：
```python
>>> type('abc') is str
True
>>> type(123) is int
True
>>> type(123) is bool
False
```

### Membership operators

C 语言中要确认某个值是否属于某个对象，需要遍历对象的成员并比较，但 Python 中提供
了简单的成员操作符，可以很方便用于确认某个值是否是某个对象的成员。

    in
    not in

注意，用于字典对象时只能用于确认键，不能用于确认值。


## Operators Precedence

优先级从高到低如下：

    { }                 Parentheses (grouping)
    f(args…)	        Function call
    x[index:index]	    Slicing
    x[index]	        Subscription
    x.attribute	        Attribute reference

    **	                Exponent
    ~x	                Bitwise not
    +x, -x	            Positive, negative
    *, /, %	            Product, division, remainder
    +, –	            Addition, subtraction

    <<, >>	            Shifts left/right
    &	                Bitwise AND
    ^	                Bitwise XOR
    |	                Bitwise OR

    in, not in,         Comparisons, membership, identity
    is, is not,
    <, <=, >, >=,
    <>, !=, ==	

    not x	            Boolean NOT
    and	                Boolean AND
    or	                Boolean OR
    lambda	            Lambda expression

## Operator Associativity

操作符的结合性：当多个操作符拥有同一个优先级时，结合性决定了操作的顺序。

几乎所有的运算符都支持从左到右的结合性：
```python
>>> 4 * 7 % 3
1
>>> 4 * (7 % 3)
4
```

只有指数(**)是从右到左的结合性：
```python
>>> 4 ** 2 ** 3
65536

>>> (4 ** 2) ** 3
4096
```

## Nonassociative Operators

非结合运算符

Python 有一些不支持结合性的运算符，比如赋值运算符和比较运算符。对于这类操作符的
排序有一些特殊的规则，这些规则不能通过结合性来管理。

例如 `5 < 7 < 9` 并不表示 `(5 < 7) < 9` 或 `5 < (7 < 9)`，而是表示 `5 < 7` 和
`7 < 9`

例如 `x = y = z` 是完全有效正确的，但 `a = b += c` 则是错误的语法
