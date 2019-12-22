+++
title = "Leetcode：#32 最长有效括号"
description = "Leetcode：#32 Longest Valid Parentheses，难度：困难"
author = "gra55"
categories = ["Leetcode"]
tags = ["动态规划", "2019"]
date = "2019-12-22"
featured = ""
featuredalt = ""
featuredpath = ""
linktitle = ""
type = "_post"
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

+ 状态：`dp[i][j]` 表示从 `i-j` 的子串是不是有效的括号
+ 状态转移方程：

```python

```

PS：动态规划的时间复杂度其实也不小（O(n²)），只是避免了很多重复的计算。
