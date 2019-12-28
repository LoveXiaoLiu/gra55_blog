+++
title = "Leetcode：#10 正则表达式匹配"
description = "Leetcode：#10 Regular Expression Matching，难度：困难"
author = "gra55"
categories = ["Leetcode"]
tags = ["动态规划", "2019"]
date = "2019-12-22"
featured = ""
featuredalt = ""
featuredpath = ""
linktitle = ""
type = "post"
+++


# 0x00 题目描述

[#10](https://leetcode-cn.com/problems/regular-expression-matching/)

给定一个字符串 s 和一个字符规则 p，实现一个支持 `.` 和 `*` 的正则表达式匹配。其中 `.` 匹配任意单个字符，`*` 匹配零个或多个前面的那一个元素。

所谓匹配，是要涵盖整个字符串 s，而不是 s 的子串。类似于 re.match 而不是 re.search。

```shell
输入: s = "aa" p = "a"
输出: false
解释: "a" 无法匹配 "aa" 整个字符串。
```

# 0x01 约束

+ s 可能为空，且只包含 a-z 的小写字母
+ p 可能为空，且只包含 a-z 的小写字母，以及字符 `.` 和 `*`

# 0x02 题解

> + 一般看到 `最长`、`最优` 等**求最优解**的题目，首先应该想到动态规划。
> 
> + **看到一个题，你首先应该能想到用什么现有的算法来解决，如果第一眼匹配不到现有的算法，那基本这个题你是解不出来的。因为不是专门研究算法的同学，能想到新的优秀的算法是很难的。**

## 算法 1：无头绪

开始拿到这个题，我感觉无从下手，想不到什么好的办法（没有头绪）。

只是想到 s 和 p 各拿一个指针，从左到右的扫描。接下来需要进行各种 if else 的判断，而且判断条件很复杂，不一定能做出来。

## 算法 2：动态规划

开始没有想到这个题还可以使用 DP。

那么什么样的问题可以使用 DP 呢？
+ 关键点是：问题可以被拆分成子问题（要不然要状态干嘛！），子问题无后效性（也就是说子问题求解出来以后，后面的计算就不用关心前面的子问题了），
+ **不要以为看到最优子结构这类问题才能用 DP。**
+ DP 的核心是缩小解的空间，暴力破解是全局解，DP 排除了不可能成为最优解的项
+ [什么是动态规划？- 徐凯强 Andy的回答 - 知乎](https://www.zhihu.com/question/23995189/answer/35324479)
+ 动态规划方法要寻找符合“最优子结构“的状态和状态转移方程的定义，在找到之后，这个问题就可以以“记忆化地求解递推式”的方法来解决。而寻找到的定义，才是动态规划的本质。

再回到这个问题上来。

那就是对正则表达式问题进行重新定义，找到状态和状态转移方程（***这个题的状态转移函数特别难找***）。
+ 状态：`dp[i][j]` 表示 `s[0:i]` 是否能匹配 `p[0:j]`，状态值是 `False` 或者 `True`
+ 转移方程：这个状态转移函数真的很难找，分为好几种情况，如下所示：
  + 最简单的情况就是 `s[i] == p[j]` 或者 `p[j] == '.'`，那就表示当前字符是匹配的，那就看 `i-1` 和 `j-1` 是否匹配，即 `dp[i][j] == dp[i-1][j-1]`
  + 复杂的是当 `p[j] == '*'`，需要分两种情况：
    + 第一种情况是看 `p[j]` 的前一个字符（`p[j-1]`）是否等于 `.` 或者与 `s[j]` 相等。此时需要判断三种情况，有一个满足要求即可：
      + `dp[i-1][j]`，这个表示 `s[:i-1]` 可以匹配 `p[:j]`，说明此时的的 `s[i]` 是匹配了多个值（即 `*` 匹配了多个值）
      + `dp[i][j-1]`，表示 `s[:i]` 与 不带星号的 `p[:j-1]` 匹配上了，说明此时的 `*` 匹配了一个字母
      + `dp[i][j-2]`，表示 `s[:i]` 与不带星号和星号前面的字符（`p[:j-2]`）匹配上了，说明此时的 `*` 匹配了 `0` 个字母
    + 剩下的就是第二种情况，`p[j-1]` 不等于 `s[j]`，即表示此时的 `*` 表示的是 `0` 个字符，那么 `dp[i][j]` 需要参考 `dp[i][j-2]` 的匹配情况

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        sLen = len(s)
        pLen = len(p)

        if sLen == 0 and pLen == 0:
            return True

        dp = [[False] * (pLen + 1) for _ in range(sLen + 1)]
        dp[0][0] = True

        # only need to init empty `s` to all `p`, because dp init to False
        for i in range(1, pLen + 1):
            if p[i-1] == '*':
                dp[0][i] = dp[0][i-2]

        for i in range(1, sLen + 1):
            for j in range(1, pLen + 1):
                if s[i-1] == p[j-1] or p[j-1] == '.':
                    dp[i][j] = dp[i-1][j-1]

                if p[j-1] == '*':
                    if s[i-1] == p[j-2] or p[j-2] == '.':
                        dp[i][j] = dp[i-1][j] or dp[i][j-1] or dp[i][j-2]
                    else:
                        dp[i][j] = dp[i][j-2]

        return dp[sLen][pLen]
```

PS：动态规划的时间复杂度其实也不小（O(n²)），只是避免了很多重复的计算。

---
参考：

:pushpin: [什么是动态规划（Dynamic Programming）？](https://www.zhihu.com/question/23995189)

:pushpin: [状态转移函数参考这里](https://leetcode-cn.com/problems/regular-expression-matching/solution/dong-tai-gui-hua-zen-yao-cong-0kai-shi-si-kao-da-b/)