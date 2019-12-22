+++
title = "Leetcode：#5 最长回文子串"
description = "Leetcode：#5 Longest Palindromic Substring，难度：中等"
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

[#5](https://leetcode-cn.com/problems/longest-palindromic-substring/)

给定一个字符串 s，找到 s 中最长的回文子串。

```shell
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

# 0x01 约束

+ s 最大长度为 1000
+ 注意字符串为空的情况

# 0x02 题解

> + 一般看到 `最长`、`最优` 等**求最优解**的题目，首先应该想到动态规划。
> 
> + **看到一个题，你首先应该能想到用什么现有的算法来解决，如果第一眼匹配不到现有的算法，那基本这个题你是解不出来的。因为不是专门研究算法的同学，能想到新的优秀的算法是很难的。**

## 算法 1：暴力破解法

这是一个蠢方法，把所有的子串找出来，判断每个子串是不是回文的，然后找出最长的那个子串返回。

找出所有子串的时间复杂度是 O(n²)。

## 算法 2：动态规划

找状态和状态转移方程：

+ 状态：`dp[i][j]` 表示从 `i-j` 的子串是不是回文的
+ 状态转移方程：`dp[i][j] = s[i] == s[j] and (j-1 <= 2 or dp[i+1][j-1] == True)`

```python
def longestPalindrome(s):
    sLen = len(s)
    if sLen == 0:
        return s
    dp = [[0] * sLen for _ in range(sLen)]

    ret = s[0]
    for j in range(1, sLen):
        # 如果使用 range(sLen) 来循环，提交会超时，可能与题目限制 s 最大为 1000 有关，多一次循环就给超时了
        for i in range(j):
            if s[i] == s[j] and (j-i <= 2 or dp[i+1][j-1]):
                dp[i][j] = 1

                if j-i+1 > len(ret):
                    ret = s[i:j+1]
    return ret
```

PS：动态规划的时间复杂度其实也不小（O(n²)），只是避免了很多重复的计算。
