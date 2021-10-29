+++
title = "Leetcode：#204 计数质数"
description = "Leetcode：#204 Count Primes，难度：简单"
author = "gra55"
date = "2019-12-22"
tags = ["leetcode", "埃拉托斯特尼筛法", "2019"]
categories = ["Algorithm"]
+++


# 0x00 题目描述

[#204](https://leetcode-cn.com/problems/count-primes/)

统计所有小于非负整数 n 的质数的数量。

# 0x01 约束

+ n 为非负整数

+ 0 和 1 不是质数

+ n 溢出的情况

# 0x02 题解

## 什么是质数？

质数就是只能被 1 和 自己整除的数字，也叫素数。

## 算法 1：暴力破解法

暴力破解法是最简单也是最容易想到的算法
+ 遍历 n 个数字，判断每个数字是不是质数，判断是不是质数的方法是遍历小于它的每个数字，看能不能被整除。

这个算法太愚蠢了，时间复杂度也很大 O(n²)。

## 算法 2：埃拉托斯特尼筛法

我最初卡在如何判断一个数字是不是质数的问题上。其实判断一个数是不是质数，不需要判断比他小的每一个数字可不可以被整除，只需要判断到 [1-sqrt(n)]。

比如说 12 = 2 x 6，12 = 3 x 4，12 = sqrt(12) x sqrt(12)，12 = 4 x 3，12 = 6 x 2。从 sqrt(12) 分割，前半部分和后半部分是对称的，所以只需要判断 [1-sqrt(n)] 区间内的数字可不可以被整除就 ok 了。

这样还是不够优化。

这里使用 “埃拉托斯特尼筛法” 来优化。
+ 算法核心是：排除法
+ 把所有小于 sqrt(n) 的质数的倍数剔除，剩下的就是质数
+ 那么为什么只需要剔除小于 sqrt(n) 的质数的倍数呢？
  + 因为大于 sqrt(n) 的合数肯定能被因式分解成有限个质数的乘积，其中最小的质数肯定在小于 sqrt(n) 的范围内。所以只需要剔除小于 sqrt(n) 的质数的倍数，就可以完全覆盖大于 sqrt(n) 的合数。

```python
def countPrimes(n):
    if n < 2:
        return 0

    isPrime = [1] * n
    isPrime[0] = isPrime[1] = 0   # 0和1不是质数，先排除掉

    # 埃式筛，把不大于根号n的所有质数的倍数剔除
    for i in range(2, int(n ** 0.5) + 1):
        if isPrime[i]:
            isPrime[i * i:n:i] = [0] * ((n - 1 - i * i) // i + 1)

    return sum(isPrime)
```

---
参考：

:pushpin: [Python最优解法](https://leetcode-cn.com/problems/count-primes/solution/pythonzui-you-jie-fa-mei-you-zhi-yi-liao-ba-by-bru/)
