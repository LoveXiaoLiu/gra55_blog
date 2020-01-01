+++
title = "Python 标准库：abc"
description = "在 Python 中 abc 模块用来创建抽象基类"
author = "gra55"
categories = ["Python"]
tags = ["python", "2019", "abc"]
date = "2019-12-31"
featured = "titanic.png"
featuredalt = "1997年12月19日詹姆斯·卡梅隆指导的《泰坦尼克号》在美国上映"
featuredpath = "date"
linktitle = ""
type = "_post"
+++

# 0x00 为什么需要 abc 模块

abc 模块为调用者和具体实现类（而不是抽象类）之间提供更高级别的语义化约定。你使用类 A 之前，类 A 就给你保证它有 b 方法和 c 属性，不需要你在使用的时候通过 getattr 来判断，这个就是约定（contract）。

上面说的那句话是什么意思其实我也不是很懂，我理解它其实就类似于静态语言中的**接口**，子类必须实现抽象基类中的所有抽象方法和属性。而 abc 模块就帮你完成了这些事情。

详情请查看 [PEP 3119](https://www.python.org/dev/peps/pep-3119/#abcs-vs-alternatives)

# 0x01 abc.ABCMeta 对象介绍

> ABCMeta 是一个元类（metaclass），用来定义 Abstract Base Classes (ABCs)
> 
> ABCMeta 可以被继承，可以作为一个 Mixin Class。
> 
> 可以注册不相关的具体类和 ABCs，作为**虚拟类**，他和他的子类会被认为是注册的 ABC 的子类（使用内置的 issubclass 函数来判断），但是注册的 ABC 不会出现在他们的 MRO（Method Resolution Order）中，注册 ABC 的方法也不能被调用。
> 
> 通过 ABCMeta 元类创建的类具有以下方法：

**register(subclass)**

+ 注册一个子类，作为这个 ABC 的虚拟子类

```python
from abc import ABCMeta

class MyABC:
    __metaclass__ = ABCMeta

MyABC.register(tuple)

assert issubclass(tuple, MyABC)
assert isinstance((), MyABC)
```

**\_\_subclasshook\_\_(subclass)**

+ 必须是一个类方法。
+ 检测 _subclass_ 是不是这个 ABC 的子类。
+ 这个方法被 \_\_subclasscheck\_\_ 调用，意思就是说可以通过这个类自定义 issubclass 的行为，不需要给每个需要注册虚拟子类的类调用 register() 方法（类似于 MyABC.register(tuple)）。
+ 这个方法必须返回 True，False 或者 NotImplemented。True 表示 _subclass_ 是 ABC 的子类，False 反之，如果是 NotImplemented，那就继续执行接下来的常规流程。

**给个例子解释下功能用法：**

+ ABC 类 MyIterable 定义了标准的可迭代对象方法（\_\_iter\_\_()）作为抽象方法。这里给出的实现仍然可以在子类调用。get_iterator() 方法也是 MyIterable 的一部分，但是它不是抽象方法，所以它没有强制要求被非抽象子类重写（overridden）。
+ 

```python
class Foo(object):
    def __getitem__(self, index):
        ...
    def __len__(self):
        ...
    def get_iterator(self):
        return iter(self)

class MyIterable:
    __metaclass__ = ABCMeta

    @abstractmethod
    def __iter__(self):
        while False:
            yield None

    def get_iterator(self):
        return self.__iter__()

    @classmethod
    def __subclasshook__(cls, C):
        if cls is MyIterable:
            if any("__iter__" in B.__dict__ for B in C.__mro__):
                return True
        return NotImplemented

MyIterable.register(Foo)
```

# 0x01 abc 模块装饰器介绍

**abc.abstractmethod(function)**

+ ss

**abc.abstractproperty([fget[, fset[, fdel[, doc]]]])**

---
参考：

:pushpin: [abc — Abstract Base Classes](https://docs.python.org/2/library/abc.html)

:pushpin: [PEP 3119](https://www.python.org/dev/peps/pep-3119/#overloading-isinstance-and-issubclass)

:pushpin: [Why use Abstract Base Classes in Python?](https://stackoverflow.com/questions/3570796/why-use-abstract-base-classes-in-python)
