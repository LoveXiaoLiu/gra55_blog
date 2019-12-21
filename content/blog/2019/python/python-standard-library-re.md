+++
title = "Python 标准库：re"
description = "Python 标准库 re（正则表达式）的用法总结"
author = "gra55"
categories = ["Python"]
tags = ["python", "2019"]
date = "2019-12-20"
featured = "titanic.png"
featuredalt = "1997年12月19日詹姆斯·卡梅隆指导的《泰坦尼克号》在美国上映"
featuredpath = "date"
linktitle = ""
type = "_post"
+++

> NOTE：
>
> + 正则表达式的 pattern 和被匹配的字符串可以是 Unicode 和 8-bit 字符串
> + 正则表示式使用反斜杠（\）来表示特殊的集合，要匹配反斜杠需要使用双反斜杠（\\）来表示。Python 中的字符串恰好也使用反斜杠（\）来表示特殊字符或集合，所以要是用 Python 字符串来表示正则表达式中的反斜杠需要使用 4 个反斜杠（\\\\）。有一个简单的方法，就是使用 Python 的 raw string（字符串前加前缀 r），raw string 内的字符串不进行任何转译。
> + 

# 0x00 正则表达式语法

这个知识点很复杂，需要单独来讨论，先给个参考链接：[Regular Expression Syntax](https://docs.python.org/2/library/re.html#regular-expression-syntax)

# 0x01 模块内容

> 这里主要讲 re 模块内的函数使用

**re.compile(pattern, flags=0)**

+ 将正则表达式的 pattern 编译成 \_sre.SRE\_Pattern 对象。
+ 通过指定 flag 的值可以改变正则表达式匹配的行为，flag 的值可以通过 `|` 操作符组合多个值。
+ re.compile() 返回的对象可以多次使用，不需要使用时再次编译，提高效率。但是，如何一个程序中只使用很少的几个正则表达式，那就不需要担心编译的问题了，因为  re.match()、re.search()、re.compile() 等都会将编译后的对象进行缓存（使用的正则表达式多了就不行了，因为毕竟缓存是有限的）。

**re.DEBUG**

+ 显示被编译的表达式的 debug 信息

**re.I && re.IGNORECASE**

+ re.I 是 re.IGNORECASE 的缩写。
+ 进行匹配的时候忽略大小写，如果需要对 Unicode 类型的字符使用这个效果，可以添加 re.UNICODE 标识

**re.L && re.LOCALE**

+ 使正则表达式中的 \w、\W、\b、\B、\s、\S 依赖于当前所在的区域。

**re.M && re.MULTILINE**



****
****
****
****
****
****
****
****
****


# 0x02 

---
参考：

:pushpin: [Regular expression operations](https://docs.python.org/2/library/re.html)
