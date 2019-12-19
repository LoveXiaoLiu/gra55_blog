+++
title = "Python 内置库：Queue"
description = "Python 内置库 Queue 的用法总结"
author = "gra55"
categories = ["Python"]
tags = ["python", "2019"]
date = "2019-12-19"
featured = "zgr-bwbj.jpeg"
featuredalt = "张国荣在《霸王别姬》中的幕后照"
featuredpath = "date"
linktitle = ""
type = "post"

+++

> Btw：Python3 中，Queue 模块已经重命名为 queue。Python3.7 中增加了一个 SimpleQueue 类，其他内容同 Python2。

## 0x00 Overview

Queue 模块实现的是**多生产者**、**多消费者**的队列。因此它是**线程安全**的，可以在多线程下使用。因为 Queue 类内部实现了所有必须的锁。

Queue 模块提供了三种类型的队列，三种类型的主要差异是获取数据的顺序不同。

## 0x01 队列

#### class：Queue(maxsize=0)

FIFO（先进先出）队列。

maxsize 用来设置队列的最大容量，一旦到达最大值，插入操作就会被阻塞住，直到队列内的内容被消费。

如果 maxsize 小于等于 0，队列的容量是无限大。

#### class：LifoQueue(maxsize=0)

LIFO（先进后出）队列，类似于栈。

其他规则同 Queue。

#### class：PriorityQueue(maxsize=0)

优先队列，内部使用 [heapq](https://docs.python.org/2/library/heapq.html#module-heapq) 实现。

其他规则同 Queue。

优先返回优先级低的数据，典型的数据模式是一个元组：(priority_number, data)

#### exception：Empty

在一个空队列调用非阻塞 get() 或者 get_nowait() 时会抛出此异常。

#### exception：Full

在一个容量达到最大值的队列调用非阻塞 put() 或者 put_nowait() 时会抛出此异常。

## 0x02 Queue 对象介绍

> LifoQueue，PriorityQueue 都是继承自 Queue

#### Queue.qsize()

返回队列的近似大小，不能保证读写

#### Queue.empty()



#### Queue.full()

#### Queue.put(item[, block[, timeout]])

#### Queue.put_nowait(item)

#### Queue.get([block[, timeout]])

#### Queue.get_nowait()

#### Queue.task_done()

#### Queue.join()

---
参考：
[Queue — A synchronized queue class](https://docs.python.org/2/library/queue.html)