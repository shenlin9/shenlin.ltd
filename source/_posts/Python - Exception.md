---
title: Python - Exception
categories:
- Python
tags:
- Python
- Exception
date: 2019-05-20 13:38:10
---

Python - Exception

<!--more-->

## Why use Exceptions?

异常不仅有助于解决诸如竞争条件之类的流行问题，而且在诸如循环、文件处理、数据库通
信、网络访问等方面的错误控制也非常有用。

异常处理是一门艺术，它为您带来编写健壮和高质量的代码的强大能力。

## Error and Exception

通常，当 Python 脚本遇到无法处理的错误情况时，就会抛出异常，也就是创建一个异常对
象。如果脚本没有立即处理这个异常，那么程序将终止并输出一个回溯到错误及其位置的跟
踪：
```python
>>> 1 / 0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
```

## try...except...else

```python
try:
	You do your operations here;
	......................
except ExceptionI:
	If there is ExceptionI, then execute this block.
	......................
except ExceptionII:
	If there is ExceptionII, then execute this block.
	......................
except (Exception3[, Exception4[,...ExceptionN]]]):
	If there is Exception3 or Exception4..., then execute this block.
	......................
except Exception as ex:
    All types of Exceptions, execute this block
	......................
else:
	If there is no exception then execute this block.
	......................
```

关于 Python 的 try 语句：
* 一个 try 语句可以根据需求有多个 except 语句，相应的 try 块可以包含抛出不同类型
  异常的语句。
* 通用 except 子句，它可以处理所有可能的异常类型，但不推荐使用，因为它可以捕
  获所有异常，但不能帮助确定是什么导致了异常发生。
* 如果 try 子句块中的代码没有引发异常，则 else 子句块中的指令将执行。

### Handle An Arbitrary Exception

处理任意异常
```python
>>> try:
...     1/0
... except:
...     print('exception occured')
...
exception occured

>>> try:
...     1/0
... except Exception:
...     print(Exception)
...
<class 'Exception'>

>>> try:
...     1/0
... except Exception as ex:
...     print(ex)
...
division by zero
```

如果您对程序可能抛出的异常没有任何线索，则此方法非常有用。

### Catch Multiple Exceptions In One Except Block

在一个 except 代码块中处理多个异常：
```python
except (Exception1, Exception2) as e:
    pass
```

例如：
```python
>>> try:
...     1/0
... except (ZeroDivisionError, IndexError) as e:
...     print(e)
...
division by zero
>>> try:
...     print('abc'[3])
... except (ZeroDivisionError, IndexError) as e:
...     print(e)
...
string index out of range
```

Python 2.6/2.7 中还可以把 as 替换为逗号，但 Python 3 中不可以：
```python
>>> try:
...     1/0
... except (ZeroDivisionError, IndexError), e:
...     print(e)
...
integer division or modulo by zero
```

### Re-Raising Exception

在 except 子句中可以只添加一个 `raise` 调用，不带任何参数。它将重新抛出捕获的异
常：
```python
>>> try:
...     1/0
... except:
...     print('catch a exception:')
...     raise
...
catch a exception:
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
ZeroDivisionError: division by zero
```

### Else Clause

只有在没有抛出异常时，才会执行 else 子句。

```python
>>> while True:
...     x = int(input('number please:'))
...
number please:Traceback (most recent call last):
  File "<stdin>", line 2, in <module>

>>> while True:
...     try:
...             x = int(input('numbers only:'))
...     except:
...             print('wrong')
...     else:
...             print(1/x)
...
numbers only:2
0.5
numbers only:3
0.3333333333333333
numbers only:a
wrong
```

### Finally Clause

finally 子句总是会执行，即使函数中在 except 块已经 return：
```python
>>> def final_test():
...     try:
...             x = 1/0
...     except:
...             print('error occured and will return')
...             return
...             print('will not execute')
...     finally:
...             print('finally will always execute')
...     print('execute or not?')
...
>>> final_test()
error occured and will return
finally will always execute
```

### Skip Through Errors And Continue Execution

理想情况下，您不应该这样做。但是如果您仍然想这样做，那么按照下面的代码检查正确的
方法：
```python
>>> try:
...     print('abc'[3])
... except IndexError as ex:
...     pass
...
```

## try...finally

不管 try 子句块是否引发异常，最后都会执行 finally 子句块，常用于释放资源：
```python
try:
   You do your operations here;
   ......................
   Due to any exception, this may be skipped.
finally:
   This would always be executed.
   ......................
```

注意：不要把 finally 和 except、else 搭配使用???
```python

```

try...except 嵌套时，异常向上传递：
当 try 块中代码引发异常时，会立即执行 finally 块。在 finally 块中的所有语句被执
行之后，异常将恢复到更高一层的 except 块执行。
```python
try:
    fob = open('test', 'r')
    try:
        fob.write("It's my test file to verify try-finally in exception handling!!"
                  )
        print 'try block executed'
    finally:
        fob.close()
        print 'finally block executed to close the file'
except IOError:
    print "Error: can\'t find file or read data"

输出：
>>finally block executed to close the file
>>Error: can\'t find file or read data
```

## Raise Exception

使用 raise 可以强制抛出一个异常，注意应放在 try 子句块里，并使用 except 捕获抛出
的异常：
```python
raise [Exception [, args [, traceback]]]
```
* Exception 异常名，如 IOError
* args      发生异常时程序用到的变量值，可用于判断哪个变量错误
* traceback 用于异常的回溯对象

注意要抛出特定类型的异常，不要抛出泛型异常：
```python
>>> try:
...     a = int(input('input a positive number:'))
...     if a <= 0:
...             raise ValueError('This is not a positive number')
... except ValueError as ve:
...     print(ve)
...
input a positive number:-3
This is not a positive number
```

## Custom Exception

自定义异常即添加一个新类，此类继承自 Exception 类，大部分内置的异常都有这样对应
的类：
```python
>>> class MyException(Exception):
...     s = 'abc'
...
>>> raise MyException('this is my own exception')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
__main__.MyException: this is my own exception
```

Example
```python
#define Python user-defined exceptions
class Error(Exception):
   """Base class for other exceptions"""
   pass

class InputTooSmallError(Error):
   """Raised when the entered alpahbet is smaller than the actual one"""
   pass

class InputTooLargeError(Error):
   """Raised when the entered alpahbet is larger than the actual one"""
   pass

#our main program
#user guesses an alphabet until he/she gets it right

#you need to guess this alphabet
alphabet = 'm'

while True:
   try:
       apb =  raw_input("Enter an alphabet: ")
       if apb < alphabet:
           raise InputTooSmallError
       elif apb > alphabet:
           raise InputTooLargeError
       break
   except InputTooSmallError:
       print("The entered alphabet is too small, try again!")
       print('')
   except InputTooLargeError:
       print("The entered alphabet is too large, try again!")
       print('')

print("Congratulations! You guessed it correctly.")
```

## Most Common Exception Errors

IOError             It occurs on errors like a file fails to open.
ImportError         If a python module can’t be loaded or located.
ValueError          It occurs if a function gets an argument of right type but
                    an inappropriate value.
KeyboardInterrupt   It gets hit when the user enters the interrupt key (i.e.
                    Control-C or Del key)
EOFError            It gets raised if the input functions (input()/raw_input())
                    hit an end-of-file condition (EOF) but without reading any
                    data.

## Built-in Exception

ArithmeticError     For errors in numeric calculation.
AssertionError	    If the assert statement fails.
AttributeError	    When an attribute assignment or the reference fails.
EOFError	        If there is no input or the file pointer is at EOF.
Exception	        It is the base class for all exceptions.
EnvironmentError	For errors that occur outside the Python environment.
FloatingPointError	It occurs when the floating point operation fails.
GeneratorExit	    If a generator’s `close()` method gets called.
ImportError         It occurs when the imported module is not available.
IOError             If an input/output operation fails.
IndexError          When the index of a sequence is out of range.
KeyError            If the specified key is not available in the dictionary.
KeyboardInterrupt   When the user hits an interrupt key (Ctrl+c or delete).
MemoryError         If an operation runs out of memory.
NameError           When a variable is not available in local or global scope.
NotImplementedError	If an abstract method isn’t available.
OSError	            When a system operation fails.
OverflowError	    It occurs if the result of an arithmetic operation exceeds the range.
ReferenceError	    When a weak reference proxy accesses a garbage collected reference.
RuntimeError	    If the generated error doesn’t fall under any category.
StandardError	    It is a base class for all built-in exceptions except `<StopIteration>` and `<SystemExit>`.
StopIteration	    The `next()` function has no further item to be returned.
SyntaxError	        For errors in Python syntax.
IndentationError	It occurs if the indentation is not proper.
TabError	        For inconsistent tabs and spaces.
SystemError	        When interpreter detects an internal error.
SystemExit	        The `sys.exit()` function raises it.
TypeError	        When a function is using an object of the incorrect type.
UnboundLocalError	If the code using an unassigned reference gets executed.
UnicodeError	    For a Unicode encoding or decoding error.
ValueError	        When a function receives invalid values.
ZeroDivisionError	If the second operand of division or modulo operation is zero.

## 

通常是调用函数的代码知道如何处理异常，而不是被调用函数本身知道如何处理异常。所以
你常常会看到 raise语句在一个函数中，try 和 except 语句在调用该函数的代码中。
