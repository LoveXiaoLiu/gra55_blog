+++
title = "Python 标准库：Queue"
description = "Python 标准库 Queue 的用法总结"
author = "gra55"
categories = ["Python"]
tags = ["python", "2019"]
date = "2019-12-19"
[[images]]
  src = "img/2019/12/titanic.png"
  alt = "1997年12月19日詹姆斯·卡梅隆指导的《泰坦尼克号》在美国上映"
  stretch = "horizontal"
+++

> Btw：
>
> Python3 中，Queue 模块已经重命名为 queue。Python3.7 中增加了一个 SimpleQueue 类，其他内容同 Python2。

# 0x00 Overview

Queue 模块实现的是**多生产者**、**多消费者**的队列。因此它是**线程安全**的，可以在多线程下使用。因为 Queue 类内部实现了所有必须的锁。

Queue 模块提供了三种类型的队列，三种类型的主要差异是获取数据的顺序不同。

# 0x01 队列

**class：Queue(maxsize=0)**

+ FIFO（先进先出）队列。

+ maxsize 用来设置队列的最大容量，一旦到达最大值，插入操作就会被阻塞住，直到队列内的内容被消费。

+ 如果 maxsize 小于等于 0，队列的容量是无限大。

**class：LifoQueue(maxsize=0)**

+ LIFO（先进后出）队列，类似于栈。

+ 其他规则同 Queue。

**class：PriorityQueue(maxsize=0)**

+ 优先队列，内部使用 [heapq](https://docs.python.org/2/library/heapq.html#module-heapq) 实现。

+ 其他规则同 Queue。

+ 优先返回优先级低的数据，典型的数据模式是一个元组：(priority_number, data)

**exception：Empty**

+ 在一个空队列调用非阻塞 get() 或者 get_nowait() 时会抛出此异常。

**exception：Full**

+ 在一个容量达到最大值的队列调用非阻塞 put() 或者 put_nowait() 时会抛出此异常。

# 0x02 Queue 对象介绍

> LifoQueue，PriorityQueue 都是继承自 Queue

**Queue.qsize()**

+ 返回队列的近似大小，不能保证读写不会被阻塞。

**Queue.empty()**

+ 判断队列是不是空，返回 True or False。不能保证读写不会被阻塞。

**Queue.full()**

+ 判断队列是不是满的，返回 True or False。不能保证读写不会被阻塞。

**Queue.put(item[, block[, timeout]])**

+ 将一个元素插入到队列中。如果 block=True 并且 timeout=None，如果队列满的话会阻塞，直到有空位置。

+ 如果 timeout 是正数 x，它会阻塞 x 秒，这段时间内如果没有空位置，则会抛出一个 Full 异常。

+ 如果 block=False，如果队列没有空位置，则会立即抛出一个 Full 异常。

**Queue.put_nowait(item)**

+ 等价于 put(item, False).

**Queue.get([block[, timeout]])**

+ 从队列移除并返回数据。如果 block=True 并且 timeout=None，如果队列为空的话会阻塞，直到有数据可用。

+ 如果 timeout 是正数 x，它会阻塞 x 秒，这段时间内如果没有数据，则会抛出一个 Empty 异常。

+ 如果 block=False，如果队列没有数据，则会立即抛出一个 Empty 异常。

**Queue.get_nowait()**

+ 等价于 get(False)

**Queue.task_done()**

+ 表示前一个排队的任务已经完成，常用于队列的消费线程。

+ 调用 get() 获取一个任务以后，紧接着调用 task_done()，就是告诉队列：正在处理的任务已经完成。

+ 如果阻塞在 join() 处，需要重新调用 task_done() 将所有任务处理完成。

**Queue.join()**

+ 调用以后就进行阻塞，直到所有的任务都被获取并处理完（task_done()）。
+ 一个消费线程调用 task_done()，未完成任务的数量就会减一。减到零以后就会停止阻塞。

---
参考：

:pushpin: [Queue — A synchronized queue class](https://docs.python.org/2/library/queue.html)
