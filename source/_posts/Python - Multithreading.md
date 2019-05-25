---
title: Python - Multithreading
categories:
- Python
tags:
- Python
- Multithreading
date: 2019-05-24 15:16:07
---

Python - Multithreading

<!--more-->

## Thread

在软件编程中，线程是具有独立指令集的最小执行单元。它是进程的一部分，在同样的上下
文中运行，共享程序的可运行资源，如内存。线程有一个起点、一个执行序列和一个结果。
它有一个指令指针，该指针保存线程的当前状态，并控制下一步以什么顺序执行什么。

## Multithreading

多线程是几乎所有高级编程语言都支持的软件编程的核心概念。
进程并行执行多个线程的能力称为多线程。
理想情况下，多线程可以显著提高任何程序的性能。
Python 多线程机制非常友好，你可以很快学会。

### Advantages Of Multithreading

* 多线程可以显著提高多处理器或多核系统上的计算速度，因为每个处理器或核心同时处理
  一个单独的线程。
* 多线程允许程序在一个线程等待输入而另一个线程同时运行 GUI 时保持响应性。此语句
  适用于多处理器或单处理器系统。
* 进程的所有线程都可以访问它的全局变量。如果一个全局变量在一个线程中发生变化，那
  么它对其他线程也是可见的。线程也可以有自己的局部变量。

### Disadvantages Of Multithreading

* 在单处理器系统上，多线程不会降低计算速度(multithreading won’t hit the speed
  of computation)。由于管理线程的开销，性能可能会降低。
* 在访问共享资源时，需要使用同步来防止互斥，这直接导致更多的内存和 CPU 利用率。
* 多线程增加了程序的复杂性，因此也使调试变得困难。
* 增加了潜在的死锁的可能性。
* 当线程不能定期访问共享资源时，可能会导致线程饥饿。然后，应用程序将无法恢复其工作。

## Multithreading Modules

Python 提供了两个模块实现线程：
* `thread`，Python 2.x 使用的线程模块，在 Python 3.x 被弃用，为了保持向后兼容性
  ，被重命名为 `_thread` 模块。
* threading

两个模块的主要区别：模块 `_thread` 使用函数的方式实现线程，而模块 `threading` 使
用面向对象的方式来实现线程。

### _thread

用以下方法生成线程
```python
thread.start_new_thread(function, args[, kwargs])
```

例如：
```python
from _thread import start_new_thread
from time import sleep

threadNum = 0

def factorial(n):

    global threadNum
    rc = 0

    if n < 1:
        threadNum += 1
        print('{}: {}'.format('Thread', threadNum))
        rc = 1
    else:
        rc = n * factorial(n-1)
        print('{}! = {}'.format(str(n), str(rc)))

    return rc

start_new_thread(factorial,(5,))
start_new_thread(factorial,(4,))
print('Waiting for thread to return ....')
sleep(2)

# 输出
Waiting for thread to return ....
Thread: 1
Thread: 2
1! = 1
1! = 1
2! = 2
2! = 2
3! = 6
3! = 6
4! = 24
4! = 24
5! = 120
```

### threading

相对于 `thread` 模块，最新的 `threading` 模块提供了对线程更丰富的特性和更好的支
持。

`threading` 模块组合了 `thread` 模块的所有方法，并额外公开了其他几个方法：
* threading.activeCount(): 活动线程对象的数量
* threading.currentThread(): 用它来确定调用方的线程控件中的线程对象的数量
* threading.enumerate(): 当前活动的线程对象的完整列表

除了上述方法，`threading` 模块还提供了 `Thread` 类，可以用来实现线程。它是
Python 多线程的面向对象变体。

`Thread` 类的方法：
* run()：它是任何线程的入口点函数
* start()：在run()方法被调用后，start()方法启动一个线程
* join([time])：使程序能够等待线程终止
* isAlive()：验证线程是否活动
* getName()：获取线程的名字
* setName()：更新线程的名字

使用 `threading` 模块实现线程的步骤：
* 创建 `threading.Thread` 类的子类
* 覆盖 `__init__(self [,args])` 方法，根据需求提供参数
* 覆盖 `run(self [，args])` 方法来编写线程的业务逻辑
* 实例化 `Thread` 子类来创建一个新线程。
* 调用 `start()` 方法来启动创建的新线程。它最终将调用 `run()` 方法来执行业务逻辑。

```python
import threading
import time

class MyThread(threading.Thread):

    def __init__(self, id):
            super().__init__() # threading.Thread.__init__(self)
            self.id = id

    def run(self):
            print('staring thread {}'.format(self.id))
            print("thread {} is doing something....".format(self.id))
            time.sleep(2)
            print('exiting thread {}'.format(self.id))

tr1 = MyThread('A')
tr2 = MyThread('B')

tr1.start()
tr2.start()

tr1.join()      # 如果没有调用 join() 方法，则程序不会等待两个线程终止
tr2.join()      # 会接着继续执行下面的 print，输出 Exiting program....，
                # 等线程终止后再输出 exiting thread A，exiting thread B

print('Exiting program....')

# 输出
staring thread A
staring thread B
thread A is doing something....
thread B is doing something....
exiting thread A
exiting thread B
Exiting program....
```

### Multithreading – Synchronizing Threads

`threading` 模块具有实现锁定的内置功能，锁定可以实现同步线程。通过锁定才能控制对
共享资源的访问，以防止损坏或丢失数据。

可以调用 `Lock()` 方法来应用锁定，它返回的是新锁定对象，可以调用锁定对象的
`acquire(block)` 方法来强制线程同步运行。

可选参数 blocking 指定线程是否等待获取锁：
* blocking = 0: 如果线程未能获得锁，则立即返回 0;如果获取锁成功，则返回 1
* blocking = 1: 线程阻塞并等待锁被释放

锁定对象的 `release()` 方法用于在不再需要锁时释放锁。

Python 的内置数据结构(如列表、字典)是线程安全的，这是使用原子字节码操作它们的副
作用。用 Python 实现的其他数据结构或基本类型(如整数和浮点数)没有这种保护。为了防
止同时访问一个对象，我们使用锁对象。

```python
import threading
import time

threadLock = threading.Lock()

class MyThread(threading.Thread):
    def __init__(self, id):
            super().__init__() # threading.Thread.__init__(self)
            self.id = id
    def run(self):
            print('staring thread {}'.format(self.id))
            threadLock.acquire()
            print("thread {} is doing something....".format(self.id))
            time.sleep(2)
            threadLock.release()
            print('exiting thread {}'.format(self.id))

tr1 = MyThread('A')
tr2 = MyThread('B')

tr1.start()
tr2.start()

tr1.join()
tr2.join()

print('Exiting program....')

# 输出：注意和上面没锁定的不同，这里是线程 A 完毕后才让线程 B 执行
staring thread A
staring thread B
thread A is doing something.... 
exiting thread A
thread B is doing something....
exiting thread B
Exiting program....
```
