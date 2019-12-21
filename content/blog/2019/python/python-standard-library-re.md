+++
title = "Python 标准库：re"
description = "Python 标准库 re（正则表达式）的用法总结"
author = "gra55"
categories = ["Python"]
tags = ["python", "2019"]
date = "2019-12-22"
featured = "beethoven.jpg"
featuredalt = "December 22 1808, Beethoven’s iconic FIFTH SYMPHONY Premieres in VIENNA"
featuredpath = "date"
linktitle = ""
type = "post"
+++

> NOTE：
>
> + 正则表达式的 pattern 和被匹配的字符串可以是 Unicode 和 8-bit 字符串
> + 正则表示式使用反斜杠（\）来表示特殊的集合，要匹配反斜杠需要使用双反斜杠（\\）来表示。Python 中的字符串恰好也使用反斜杠（\）来表示特殊字符或集合，所以要是用 Python 字符串来表示正则表达式中的反斜杠需要使用 4 个反斜杠（\\\\）。有一个简单的方法，就是使用 Python 的 raw string（字符串前加前缀 r），raw string 内的字符串不进行任何转译。

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
+ 如果 maxsplit 非 0，则匹配到第 maxsplit 个就停止，将剩余的字符串追加到列表后面，然后返回。
+ 如果在字符串的开始捕获到正则表达式中的组（groups，圆括号中的内容），则返回的列表第一个元素是空字符串，对于字符串的末尾也是一样的。

```python
re.split('\W+', 'Words, words, words.')
# ['Words', 'words', 'words', '']
re.split('(\W+)', 'Words, words, words.')
# ['Words', ', ', 'words', ', ', 'words', '.', '']
re.split('\W+', 'Words, words, words.', 1)
# ['Words', 'words, words.']
re.split('[a-f]+', '0a3B9', flags=re.IGNORECASE)
# ['0', '3', '9']
re.split('(\W+)', '...words, words...')
# ['', '...', 'words', ', ', 'words', '...', '']
```

**re.findall(pattern, string, flags=0)**

+ 以列表的形式，返回所有不重叠的匹配。从左到右扫描字符串，匹配到的字符串按先后顺序返回。
+ 注意，如果 pattern 中包含组（groups，圆括号中的内容），则返回组列表；如果有多个组，则返回的列表的每一项都是一个元组

```python
re.findall("a?bc", "bcddabcessbbc")
# ['bc', 'abc', 'bc']
re.findall("a?(bc)", "bcddabcessbbc")
# ['bc', 'bc', 'bc']
re.findall("(a?)(bc)", "bcddabcessbbc")
# [('', 'bc'), ('a', 'bc'), ('', 'bc')]
```

**re.finditer(pattern, string, flags=0)**

+ 返回一个迭代器，这个迭代器会生成  MatchObject 对象，不会重叠匹配。
+ re.finditer 其实是 re.findall 的迭代器版本（类似于 range 和 xrange）

**re.sub(pattern, repl, string, count=0, flags=0)**

+ 用 repl 替换最左边的非重叠的匹配项，然后返回该字符串。如果没有匹配到，就返回原字符串。
+ repl 可以是字符串或者函数。
  + 如果是字符串，任何 backslash escapes 都会被处理（`\n` 会被认为是换行，不认识的转义会忽略）。特殊情况，`\1` 会拿匹配到的第一个组来替换到 `\1` 位置（叫做 backreferences）。`\g<2>` 等同于 `\2`，`\g<name>` 使用的是命名的组 `(?P<name>...)`，`\g<0>` 使用的是整个字符串。
  + 如果是函数，每次非重复匹配都会调用该函数，函数参数是匹配到的 MatchObject 对象，但会一个替换的字符串。
+ count 表示要替换的最大次数，必须是非负值，如果是 0，所有的匹配项都被替换。
+ Empty matches for the pattern are replaced only when not adjacent to a previous match, so sub('x*', '-', 'abc') returns '-a-b-c-'.

```python
# repl is string
re.sub(r'def\s+([a-zA-Z_][a-zA-Z_0-9]*)\s*\(\s*\):',
        r'static PyObject*\npy_\1(void)\n{',
        'def myfunc():')
#'static PyObject*\npy_myfunc(void)\n{'

# repl is function
def dashrepl(matchobj):
    if matchobj.group(0) == '-': return ' '
    else: return '-'
re.sub('-{1,2}', dashrepl, 'pro----gram-files')
#'pro--gram files'
re.sub(r'\sAND\s', ' & ', 'Baked Beans And Spam', flags=re.IGNORECASE)
#'Baked Beans & Spam'
```

**re.subn(pattern, repl, string, count=0, flags=0)**

+ 与 re.sub 操作相同，只不过返回的是元祖 `(new_string, number_of_subs_made)`，新产生的字符串和替换的次数。

**re.escape(pattern)**

+ 转义所有字符（除了 ASCII 字符和数字）。

```python
print re.escape('python.exe')
# python\.exe

legal_chars = string.ascii_lowercase + string.digits + "!#$%&'*+-.^_`|~:"
print '[%s]+' % re.escape(legal_chars)
# [abcdefghijklmnopqrstuvwxyz0123456789\!\#\$\%\&\'\*\+\-\.\^\_\`\|\~\:]+

operators = ['+', '-', '*', '/', '**']
print '|'.join(map(re.escape, sorted(operators, reverse=True)))
# \/|\-|\+|\*\*|\*
```

**re.purge()**

+ 清除正则表达式缓存。

**exception: re.error**

+ 当传递的正则表达式是非法的，或者在编译、匹配中发生错误，都会抛出此异常。

**class: re.RegexObject**

+ 也就是 re.compile 产生的对象，[re.RegexObject](https://docs.python.org/2/library/re.html#regular-expression-objects) 实例的所有方法，和上边提到的同名函数功能类似（上边的函数内部其实就是先初始化一个 [re.RegexObject](https://docs.python.org/2/library/re.html#regular-expression-objects) 对象，然后调用它的同名方法），只是有细微的差别，这里不过多描述。

# 0x02 Match Objects

> 这里重新写一个章节来介绍 MatchObject，因为他是上边大部分函数的返回值，MatchObject 对象内部的方法和属性还是需要着重介绍一下的。
> 
> MatchObject 的 bool 值总是 True，因此可以直接用来做 if 判断（因为 search()、match() 等如果没匹配到会返回 None）

**expand(template)**

+ template 可以通过 backreferences 来获取匹配到的值，作用类似于 re.sub() 那样。

```python
a=re.search("a(bb)", "cabb")
a.expand("www.\g<1>.com")
# 'www.bb.com'
```

**group([group1, ...])**

+ 返回一个或多个匹配到的子组。如果只有一个参数，那么返回值也是一个字符串；如果是多个参数，那么返回一个元组，每个参数代表元组中的一项；如果没有参数，group1 默认是 0，即返回整个匹配到的内容（不管 group 几是 0，对应的元组项都会返回整个匹配到的字符串）。
+ 如果传递的参数是负值或者超过组的长度，则会抛出 IndexError 异常。
+ 如果 pattern 中的某一个 group 没有匹配到内容，那么它对应的返回值就是 None；如果匹配到多次，那它会返回最后一次匹配到的值。
+ 如果 pattern 中使用命名的组（`(?P<name>...)`），那么参数可以是索引也可以是字符串，如果没有找到字符串对应的组，则会抛出 IndexError 异常。

```python
m = re.match(r"(\w+) (\w+)", "Isaac Newton, physicist")
m.group(0)       # The entire match
# 'Isaac Newton'
m.group(1, 2)    # Multiple arguments give us a tuple.
# ('Isaac', 'Newton')

m = re.match(r"(?P<first_name>\w+) (?P<last_name>\w+)", "Malcolm Reynolds")
m.group('first_name')
# 'Malcolm'
m.group('last_name')
# 'Reynolds'
```

**groups([default])**

+ 返回一个元组，元组包含所有匹配到的子组（从 1 开始到最后一个组）。
+ default 参数用于指定没有匹配到的组的默认值。

```python
m = re.match(r"(\d+)\.?(\d+)?", "24")
m.groups()      # Second group defaults to None.
# ('24', None)
m.groups('0')   # Now, the second group defaults to '0'.
# ('24', '0')
```

**groupdict([default])**

+ 返回一个字典，key 是命名组的名称，value 是匹配到的内容。非命名的组不会被返回。
+ default 参数同 groups()

```python
m = re.match(r"(?P<first_name>\w+) (\w+)", "Malcolm Reynolds")
m.groupdict()
# {'first_name': 'Malcolm'}
```

**start([group]) && end([group])**

+ 返回与 group 参数匹配的子字符串的开始和结束的索引。默认 group 是 0，意思就是匹配的整个字符串。
+ 如果匹配到的组是一个空字符串，那么 m.start(groupN) == m.end(groupN)。

```python
email = "tony@tiremove_thisger.net"
m = re.search("remove_this", email)
email[:m.start()] + email[m.end():]
# 'tony@tiger.net'
```

**span([group])**

+ 返回 2-tuple (m.start(group), m.end(group))。默认 group 为 0，即返回整个匹配到的内容。
+ 注意，如果 group 没有参与匹配，则为 (-1, -1)。

**pos**

+ 传递给 RegexObject 对象的 search () 或 match () 方法的 pos 值。 
+ 表示正则表达式匹配引擎执行的开始位置

**endpos**

+ 传递给 RegexObject 对象的 search () 或 match () 方法的 endpos 值。
+ 表示正则表达式匹配引擎执行的结尾位置

**lastindex**

+ 最后一个捕获匹配的组的索引，如果没有匹配到组，则返回 None。
+ 针对 ab 字符串，pattern（(a)b、((a)(b))、((ab))）中 lastindex == 1，pattern（(a)(b)）中 lastindex == 2

**lastgroup**

+ 最后一个捕获匹配的组的名称，如果组没有名称或者根本没有匹配到组，则返回 None。

**re**

+ re 是一个 正则表达式对象，它的 match() 或 search() 方法产生 MatchObject 实例

**string**

+ 传递给 match() 或 search() 的字符串。

---
参考：

:pushpin: [Regular expression operations](https://docs.python.org/2/library/re.html)
