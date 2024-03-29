+++
title = "基础算法：分治法"
description = "常用的基础算法之一，用于将很难处理的大问题，拆解成小问题处理，然后合并小问题的解，得到大问题的解"
author = "gra55"
categories = ["Algorithm"]
tags = ["基础算法", "分治法", "2020"]
date = "2020-01-14"
+++

# 0x00 算法概述

分治法的思想就是将不可能或者很难解决的问题，拆解成多个相似的子问题，然后将子问题拆解成更小粒度的子问题，直到这些小问题可以很容易的被解决，然后合并这些小问题的解以得到最终的解。

分治法是很多高效算法的基础，例如快排，归并排序等

# 0x01 解题步骤

满足以下所有特征才能使用分治法：
+ 该问题缩小到一定规模，就可以很容易的解决
+ 具有最优子结构，即小规模问题和大规模问题是相同的问题（其实就是递归思想，可以使用递归来解决）
+ 子问题的解可以合并成为大问题的解（这是关键特性，如果不满足，可以使用动态规划或贪心算法）
+ 子问题之间是独立的（不独立的话也可以使用分治法，但是就是比较复杂，需要处理公共的子问题）

步骤：
1. 分解：将原问题分解为若干个规模较小，相互独立，与原问题形式相同的子问题；
2. 解决：若子问题规模较小而容易被解决则直接解，否则递归地解各个子问题
3. 合并：将各个子问题的解合并为原问题的解。

# 0x02 实现方式

+ 递归

---
参考：

:pushpin:  [五大常用算法之一：分治算法](https://www.cnblogs.com/steven_oyj/archive/2010/05/22/1741370.html)
