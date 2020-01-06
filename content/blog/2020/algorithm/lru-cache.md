+++
title = "LRU 算法概述"
description = "LRU 算法是常用的缓存算法：最近最少被使用的缓存项将被淘汰掉"
author = "gra55"
categories = ["algorithm"]
tags = ["lru", "2020"]
date = "2020-01-06"
featured = ""
featuredalt = ""
featuredpath = ""
linktitle = ""
type = "post"
+++

# 0x00 算法概述

LRU（Least-recently-used）：最近最少被使用的某个东西。最早使用在内存中，表示最近最少被使用的内存将会被释放。

# 0x01 算法实现

如果想要这个算法的 get 和 set 时间复杂度都为 O(1)，我们这里需要使用两种数据结构 dict 和双向链表。

dict 获取元素的时间复杂度是 O(1)，set 元素时通过字典先找到已存在的元素，然后将这个元素挪到链表的尾部，时间复杂度也是 O(1)。结构如下所示：

```python
        +----------+      +----------+     +----------+       +----------+
        | key-root |      |  key-A   |     |   key-B  |       |   key-C  |
        +----------+      +----+-----+     +----+-----+       +----+-----+
             |                 |                |                  |
             |                 |                |                  |
             v                 v                v                  v
        +----+-----------------+----------------+------------------+----------+
        |                          hash function                              |
        +----+-----------------+----------------+------------------+----------+
             |                 |                |                  |
             v                 v                v                  v
        +----+-----+      +----+-----+      +---+------+      +----+-----+
+------>+          +----->+          +------+          +----->+          +--------+
|       |   root   |      |     A    |      |     B    |      |     C    |        |
|  +----+          +<-----+          +------+          +<-----+          +<---+   |
|  |    +----------+      +----------+      +----------+      +----------+    |   |
|  |                                                                          |   |
|  +--------------------------------------------------------------------------+   |
+---------------------------------------------------------------------------------+
```

# 0x02 代码实现

在 Python 3 的内置模块 functools 中（Python 2 中没有），给出了 [lru_cache 的实现](https://github.com/python/cpython/blob/3.7/Lib/functools.py)，这应该就是用 Python 来实现 lru cache 的最佳实践了。

有兴趣的可以看看源码，其实很简单，就是使用 0x01 中介绍的两种数据结构来实现的。

另外 Python 中有一种数据类型是有序字典（[OrderedDict](https://docs.python.org/2.7/library/collections.html#ordereddict-objects)，来自 collections 模块），也可以直接使用 OrderedDict 来实现 lru cache，其实 OrderedDict 的原理和上一种方式一样，只是将字典和双向链表的实现交给了 OrderedDict。

---
参考：

:pushpin:  [lru_cache 的实现](https://github.com/python/cpython/blob/3.7/Lib/functools.py)
