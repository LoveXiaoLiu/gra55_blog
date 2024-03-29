+++
title = "基础算法：动态规划"
description = "常用的基础算法之一，思想类似于分治法，不同点是子问题之间存在依赖，用来处理多阶段决策类问题"
author = "gra55"
categories = ["Algorithm"]
tags = ["基础算法", "动态规划", "2020"]
date = "2020-01-14"
+++

# 0x00 算法概述

动态规划的思想类似于分支法，也是将待处理的问题拆分成多个子问题，按顺序求解子问题，前一个子问题的解为后一个子问题的求解提供了有用的信息。

# 0x01 解题步骤

满足以下所有特征才能使用动态规划：
+ 最优子结构：问题的最优解所包含的子问题的解也是最优的（因为只有这样，状态转移到最后得出的解才是全局最优解）
+ 无后效性：子问题的状态一旦确定，不会受到后面子问题的影响
+ 有重叠子问题：子问题之间不是独立的，后面的子问题会多次用到前面子问题的解（这个不是必要条件，这个条件会使动态规划算法更具优势）

步骤：

> 动态规划一般用来处理多阶段决策类问题，一般由初始状态开始，通过中间阶段的决策，最后得到结束的状态。这样会实现一个决策序列：`初始状态 → │决策1│ → │决策2│ →…→ │决策n│ → 结束状态`

1. 划分阶段：按照问题特征，把问题划分成多个阶段，阶段必须是有序的或者可排序的，否则无法求解
2. 确定状态：一般情况下，各阶段状态就是各子问题的解，这样才能推出最终的解
3. 状态转移方程：由之前一个或者多个状态得出当前状态的公式（这个一般是最难找的）
4. 寻找终止条件：状态转移方程一般是递推式，需要找到终止条件来结束程序

# 0x02 实现方式

动态规划最难的就是找**状态**和**状态转移方程**，有时候**状态**找不对就很难找到**状态转移方程**。

代码实现起来也不是很复杂。

---
参考：

:pushpin:  [五大常用算法之二：动态规划算法](https://www.cnblogs.com/steven_oyj/archive/2010/05/22/1741374.html)
