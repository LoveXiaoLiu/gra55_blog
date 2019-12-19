+++
title = "抛弃 Python2，从此使用 Python3"
description = "Python3 "
author = "gra55"
categories = ["Python"]
tags = ["python", "2019"]
date = "2019-12-11"
featured = "kobe_first_champ.jpg"
featuredalt = "Jerry Buss is interviewed after his team won the 1999-2000 NBA Finals"
featuredpath = "date"
linktitle = ""
type = "_post"
+++

> 为什么要使用 Python3？我 2 用的好好的。（py 版本演进过程；2018 年开发者问卷调查；）
> 
> Python 2 3 的特性对比
> 
> 到底写 2 还是写 3，还是两个都写。（如果是自己的项目，只写 py3，如果是提供的第三方库，需要写 3 代码来兼容 2。为什么不是写 2 的代码兼容 3 呢？2 孔的插座好比 Python2，3 孔的好比 Python3，本来是 2 孔的充电器插 2 孔的电源，3 孔的插 3 孔的。现在假如 2 孔的插座快淘汰了，你是愿意给 3 孔的充电器安装一个 2 孔的转接头，让他可以用使用 2 孔的插座呢，还是另外一种做法）
> 
> 如何逐步迁移到 Python 3（不迁移的话，你永远在使用 py2，因为你迈不出第一步）（需要注意很多老库不支持 py3）
> 
> 迁移工具有哪些
> 
> 如何部署 Python 2 和 Python3 的环境，并且还有虚拟环境

# 0x00 为什么要升级到 Python3

Python2
+ Python2 算是一种比较古老的语言
+ 2010 年 7 月，发布 2.7.0 版本的时候，就宣布了以后不会再发布 Python 2.x 大版本。参考 [PEP 404](https://www.python.org/dev/peps/pep-0404/)
+ 2020 年 1月 1 日以后，Python 官方不再支持 Python2。最初是打算 2014 年停止支持，发现大多数人还是没有迁移到 Python3，所以推迟到 2020 年。参考 [PEP 373](https://www.python.org/dev/peps/pep-0373/)

Python3
+ Python3 是一种比较现代化的语言
+ 第一个版本 3.0.0，发布于 2008 年 12 月
+ 初期的 3.x 版本不稳定，直到 3.3 版本（2012 年 9 月发布）以后

Python2 语言在最初设计的时候，就存在很多问题（Unicode、print & exec 都是语句、整数使用了地板除、rang等），在后来版本迭代的时候发现向后兼容的成本大于新写一门语言，所以就果断放弃了 Python2。

为什么要推迟停止支持 Python2 的时间？我个人觉得是 Python3 早期没有太多的库来支持，如果我的项目想迁移到 Python3，但是我依赖的库只支持 Python2，那我肯定是放弃升级 Python3 的。

But now，时代不一样了，95% 的常用库都已经支持 Python3 了，所以，你还在等什么呢？

另外，根据 [2017](https://www.jetbrains.com/research/python-developers-survey-2017/) 和 [2018](https://www.jetbrains.com/research/python-developers-survey-2018/) 年的 Python 开发者调查报告显示，2018 年使用 Python3 的人数高达 84%，2019 年的报告还没出来，占比肯定 会更高。

# 0x01 主要差异对比

| |Python2 |   Python3|
| --- | --- | --- |
| print | print 是语句 | 函数 |
| 整数除法 | 地板除 | 返回浮点型 |
| input | 转换成输入的类型 | 传入的值总是文本类型（类似 Python2 中的 raw_input） |
| Round | 返回浮点型数字 | 返回指定的精度 |
| 变量泄漏 | 存在变量泄漏 | 不存在 |
| range | 直接返回列表 | 返回生成器（类似 Python2 中的 xrange） |
| Exception | as 关键字可有可无 | as 关键字必须存在 |
| 生成器的 .next 方法 | 可以使用 | 不能使用 |
| ASCII & Unicode & Byte | 有 ASCII string 和 Unicode 类型，无 Byte 类型 | 有 Unicode 和 Byte 类型 |

不再返回 list：
+ zip()
+ map()
+ filter()
+ dictionary’s .keys() method
+ dictionary’s .values() method
+ dictionary’s .items() method

# 0x02 如何写兼容 Python2 & 3 的代码

> 首先需要明确一个问题：写 Python2 代码来兼容 Python3，还是写 Python3 代码来兼容 Python2？
> + 历史经验来看，最佳实践是写**Python3 代码来兼容 Python2**
>
> + 打一个不太恰当的比方：2 孔的插座好比 Python2 解释器，3 孔的好比 Python3 解释器，本来是 2 孔的充电器（Python2 代码）插 2 孔插座（Python2 解释器），3 孔（Python3 代码）的插 3 孔插座（Python3 解释器）。现在假如 2 孔的插座（Python2 解释器）快被淘汰了，你是愿意给 3 孔的充电器（Python3 代码）安装一个 2 孔的转接头（six library），让他可以同时使用 2 孔（Python2 解释器）和 3 孔插座（Python3 代码）呢？还是给 2 孔充电器（Python2 代码）安装一个 3 孔的转接头（six library）？
>
> + 当然是给 3 孔的充电器（Python3 代码）安装 2 孔的转接头（six library）。因为 2 孔的插座（Python2 解释器）被淘汰以后，我们完全可以不使用转接头了。否则必须一直依赖转接头（six library）。

[python-future](https://github.com/PythonCharmers/python-future) 库是用来弥补 Python2 和 Python3 兼容性的库，也就是一个转接头。

[six](https://github.com/benjaminp/six) 也是一个解决 Python2 和 Python3 兼容性的库

Python 内置 \_\_future\_\_ 

这三者的区别：
+ \_\_future\_\_ Python 内置的兼容模块，提供基础的兼容特性（print 函数、Unicode 等）

# 0x03 升级工具



# 0x04 安装 Python 2 & 3 的环境

---
参考：

:pushpin: [Python 2 or 3?](https://www.fullstackpython.com/python-2-or-3.html)

:pushpin: [Should I use Python 2 or Python 3 for my development activity?](https://wiki.python.org/moin/Python2orPython3?action=recall&rev=96)

:pushpin: [PEP 373 -- Python 2.7 Release Schedule](https://www.python.org/dev/peps/pep-0373/)

:pushpin: [Why was Python 3 made incompatible with Python 2?](http://python-notes.curiousefficiency.org/en/latest/python3/questions_and_answers.html#why-was-python-3-made-incompatible-with-python-2)

:pushpin: [Cheat Sheet: Writing Python 2-3 compatible code](http://python-future.org/compatible_idioms.html)

:pushpin: [Python 2 vs Python 3: Key Differences](https://www.guru99.com/python-2-vs-python-3.html)

:pushpin: [Python2 vs Python3 | Syntax and performance Comparison](https://www.geeksforgeeks.org/python2-vs-python3-syntax-and-performance-comparison/)

:pushpin: [Best Practices for Compatible Python 2 and 3 Code](https://pybit.es/python-porting.html)

:pushpin: [The key differences between Python 2.7.x and Python 3.x with examples](http://sebastianraschka.com/Articles/2014_python_2_3_key_diff.html)

:pushpin: [Writing cross-compatible Python 2/3: Difference between \_\_future\_\_, six, and future.utils?](https://stackoverflow.com/questions/42110826/writing-cross-compatible-python-2-3-difference-between-future-six-and-fut)