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

+ 指定正则匹配时进行多行匹配：`^` 字符匹配字符串和**每一行开始（也就是换行符之后）**的位置（默认情况下，`^` 只匹配字符串开始的位置），`$` 字符匹配字符串和**每一行结尾（也就是换行符之前）**的位置（默认情况下，`$` 只匹配字符串结尾，如果结尾是换行符就匹配到换行符之前的位置）

**re.S && re.DOTALL**

+ 这个标识使正则表达式中的 `.` 字符可以匹配任何字符（包括换行符，默认不匹配换行符）

**re.U && re.UNICODE**

+ 使 \w、\W、\b、\B、\d、\D、\s、\S 依赖于 Unicode 字符集，可以为 re.IGNORECASE 开启 non-ASCII 的匹配

**re.X && re.VERBOSE**

+ 这个标识允许你给正则表达式的 pattern 添加注释，注释使用 `#` 关键字，从最左边的 `#` 字符开始到行尾都是注释（前提是 `#` 不在 character class 中，或者前边没有转义反斜杠）。pattern 中的空白字符是被忽略的，除非在 character class 中，或者前边是一个非转义的反斜杠，或者在 `*?`、`(?:`、`(?P<...>` 中。

```python
# 这两个正则表达式等价
a = re.compile(r"""\d +  # the integral part
                   \.    # the decimal point
                   \d *  # some fractional digits""", re.X)
b = re.compile(r"\d+\.\d*")
```

**re.search(pattern, string, flags=0)**

+ 扫描字符串（string），寻找第一个正则表达式匹配到的位置，返回一个 MatchObject 对象。如果在字符串中没有位置匹配，就返回 None。注意，this is different from finding a zero-length match at some point in the string（没太明白这句话什么意思）

```python
a=re.search("a?bb", "bbcc")
a.group()
# 'bb'

a=re.search("a?bb", "bccabbcbb")
a.group()
# 'abb'
```

**re.match(pattern, string, flags=0)**

+ 其他的内容与 re.search 相同，不同的是， re.match 必须从字符串的开头开始匹配（re.search 可以从中间才开始匹配正则表达式）。
+ 还有一点需要注意的是，即使在 re.MULTILINE 模式下，re.match 也只能在字符串的开头匹配（而不是每行的开头）

**re.split(pattern, string, maxsplit=0, flags=0)**

+ 从 pattern 匹配到的位置来分割字符串。
+ 如果发现在 pattern 中有圆括号，会同时将所有的 groups 添加到列表中返回。

****
****
****
****
****


# 0x02 

---
参考：

:pushpin: [Regular expression operations](https://docs.python.org/2/library/re.html)
