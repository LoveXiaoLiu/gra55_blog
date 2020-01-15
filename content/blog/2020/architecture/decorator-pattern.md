+++
title = "设计模式 # 结构型 # 装饰器模式"
description = "结构型设计模式之一，允许向一个现有对象添加新功能，同时又不改变其结构"
author = "gra55"
categories = ["架构"]
tags = ["设计模式", "结构型设计模式", "装饰器模式", "2020"]
date = "2020-01-15"
featured = ""
featuredalt = ""
featuredpath = ""
linktitle = ""
type = "post"
+++

> 结构型设计模式主要用于描述**对象之间的组合**，通过对象间的组合来完成特定功能。

# 0x00 模式概述

装饰器模式会动态的给一个对象添加新功能，就增加功能来说，装饰器模式比子类化更灵活（[合成复用原则](https://www.yangliu.date/blog/2019/architecture/design-patterns-overview/)）。


# 0x01 场景

当需要给一个对象增加新功能时，又不想使用继承，可以考虑使用装饰器模式实现。

# 0x02 解决方案

装饰器模式通过将对象包装在装饰器类内部来动态更改对象的行为。

# 0x03 总结

装饰器模式很简单，大多数脚本语言已经在语言层面实现了该模式。

---
参考：

:pushpin: [设计模式（45种）](https://github.com/guanguans/notes/blob/master/%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%EF%BC%8845%E7%A7%8D%EF%BC%89.md)

:pushpin: [Design patterns for humans 中文版](https://www.guanguans.cn/design-patterns-for-humans-cn/)