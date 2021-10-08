+++
title = "Leetcode：#32 最长有效括号"
description = "Leetcode：#32 Longest Valid Parentheses，难度：困难"
author = "gra55"
date = "2019-12-23"
tags = ["leetcode", "动态规划", "2019"]
categories = ["algorithm"]
+++


# 0x00 题目描述

[#32](https://leetcode-cn.com/problems/longest-valid-parentheses/)

给定一个只包含 `(` 和 `)` 的字符串，找出最长的包含有效括号的子串的长度。

```shell
输入: "(()"
输出: 2
解释: 最长有效括号子串为 "()"

输入: ")()())"
输出: 4
解释: 最长有效括号子串为 "()()"
```

# 0x01 约束

+ 注意字符串为空的情况

# 0x02 题解

> + 一般看到 `最长`、`最优` 等**求最优解**的题目，首先应该想到动态规划。
> 
> + **看到一个题，你首先应该能想到用什么现有的算法来解决，如果第一眼匹配不到现有的算法，那基本这个题你是解不出来的。因为不是专门研究算法的同学，能想到新的优秀的算法是很难的。**

## 算法 1：无头绪

之前遇到一个类似的题，没有任何头绪。暴力破解肯定是能想出来的，但是是蠢办法。

## 算法 2：动态规划

找状态和状态转移方程（类似于[最长回文子串](https://www.yangliu.date/blog/2019/leetcode/leetcode-5-longest-palindromic-substring/)）：

+ 状态：
  + ~~`dp[i][j]` 表示从 `i-j` 的子串是不是有效的括号~~
  + 第一次就将状态找错了，DP 也可以是一维的三维的，不要形成思维定式。这里就是一维的状态：dp[i] 中 i 表示在 s 中，下标以 i 结束的子串，有效括号的个数
+ 状态转移方程：
  + DP 最不好找的就是转移方程
  + 这里分 3 种情况：
    + 以 `(` 结尾的肯定不是有效括号，直接忽略处理
    + 以 `)` 结尾的字符串中，如果 i-1 是 `(`，那么 dp[i] = dp[i-2] + 2（前两个字符索引处对应的最长括号有效个数 + 新增的 1 个有效括号）
    + 以 `)` 结尾的字符串中，如果 i-1 是 `)`，那么久追溯到 i-1 索引处对应的有效括号的最左端，即 i-dp[i-1]，然后查看 i-dp[i-1]-1是不是 `(`，如果是的话，dp[i] = dp[i-1]+2+dp[i-dp[i-1]-2]，其中 dp[i-dp[i-1]-2] 是前面的有效括号个数

```python
def longestValidParentheses(s):
    sLen = len(s)
    if sLen < 2: return 0
    dp = [0] * sLen

    for i in range(1, sLen):
        if s[i] == ')':
            if s[i-1] == '(':
                tmp = 0 if i-2 < 0 else dp[i-2]
                dp[i] = tmp + 2
            else:
                if i-dp[i-1]-1 >=0 and s[i-dp[i-1]-1] == '(':
                    dp[i] = dp[i-1] + 2 + dp[i-dp[i-1]-2]

    return max(dp)
```

PS：动态规划的时间复杂度其实也不小（O(n²)），只是避免了很多重复的计算。
