+++
title = "Python 基础知识点"
description = "总结 Python 中常用的基础知识点"
author = "gra55"
categories = ["Python"]
tags = ["python", "2020", "基础"]
date = "2020-01-03"
featured = "2020-year-first-snow.jpg"
featuredalt = "下雪的早晨等待公交车上班的人们，拍摄于2020年第一场雪"
featuredpath = "date"
linktitle = ""
type = "post"
+++

> 以下知识点只给出结论，不给予论证，请自行测试。

# 0x00 实例属性的访问顺序

+ `__getattribute__`
+ 类的数据描述符属性
+ 实例的属性
+ 类的非数据描述符属性
+ 类的普通属性
+ `__getattr__`

# 0x01 实例属性的赋值顺序

+ 类的数据描述符属性
+ 实例属性

# 0x02 内存管理

+ 引用计数
+ 内存池
+ 垃圾回收

# 0x03 垃圾回收

+ 引用计数
+ 标记清除
+ 分代回收

# 0x04 作用域

> global 关键字用来访问全局作用域的变量值。
> 
> nonlocal 关键字（只有 Python 3 支持）用来访问 Enclosing locals（闭包）作用域的变量值。
> 
> Python 没有块作用域，for 语句范围不是单独的作用域。

+ Local
+ Enclosing locals
+ Global
+ Built-in

# 0x05 is 关键字

+ is 用来比较变量的地址
+ == 用来比较变量的值

# 0x06 单引号/双引号/三引号

+ 单引号和双引号等效，换行需要使用反斜杠（\\）
+ 三引号可以直接换行，可以包含任何形式的字符串

# 0x07 自省

+ 运行时能够查看对象内部的属性或状态
+ 自省函数：type()、isinstance()、dir()、hasattr()、getattr()

# 0x08 staticmethod & classmethod

+ staticmethod & classmethod 都是内置类型，属于非数据描述符
+ 可以将类内的方法转变成静态方法和类方法（从 Python 2.2 以后才有）

# 0x09 单下划线 & 双下划线

+ `__foo__`：一种约定，Python 内部的名字，用来区别其他用户自定义的命名，以防冲突。
+ `_foo`：一种约定，用来指定变量私有，其实 Python 解析器没有真正的对其进行私有，只是用来提示程序员此变量是私有变量。
+ `__foo`：这个是间接的实现私有，解析器用`_classname__foo`来代替这个名字，以区别和其他类相同的命名。注意：继承的时候双下划线属性是不继承的，因为它会被重命名为`_classname__foo`。

# 0x10 AOP 编程

+ 面向切面编程：在运行时，动态地将代码切入到类的指定方法、指定位置上的编程思想就是[面向切面编程](https://www.cnblogs.com/Wolfmanlq/p/6036019.html)。
+ 装饰器就是一种 AOP 编程思想

# 0x11 鸭子类型

+ 鸭子类型是针对不同类型对象具有相同行为的一种统称。

---
参考：

:pushpin: [InterV For Python](https://www.jianshu.com/p/507f98564c71)
