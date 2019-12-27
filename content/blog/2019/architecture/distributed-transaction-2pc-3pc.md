+++
title = "分布式事务：2PC & 3PC"
description = "单体架构演化到微服务架构，分布式事务是必须要解决的问题"
author = "gra55"
categories = ["架构"]
tags = ["分布式事务", "2PC", "3PC", "2019"]
date = "2019-12-27"
featured = "great-wall-station-antarctica.jpg"
featuredalt = "1984年12月27日，我国首支极地探险队在南极半岛附近的乔治王岛登陆，开始建设长城站"
featuredpath = "date"
linktitle = ""
type = "post"
+++

> 什么是分布式？
> 
> + 与分布式对立的概念是单体系统，分布式系统是将不同的功能模块拆分成不同的服务，微服务就是一个典型的分布式系统。
>
> 分布式事务常见解决方案:
> 
> + 2PC两段提交协议
> + 3PC三段提交协议(弥补两端提交协议缺点)
> + TCC或者GTS(阿里)
> + 消息中间件最终一致性
> + 使用LCN解决分布式事物，理念“LCN并不生产事务,LCN只是本地事务的搬运工”。

# 0x00 CAP 定理

所谓定理就是别人发现的规律，并且已经证实该规律是正确的（就像勾股定理，它永远是正确的，你直接用它的结论就行，你也可以自己证实其正确性）。

CAP 定理定义：在异步网络中（不可靠的网络），不可能同时实现并发读/写状态下的 availability、consistency。即 Consistency、Availability 和 Partition Tolerance 不能同时满足。

[这里证明了 CAP 是成立的](https://zhuanlan.zhihu.com/p/33999708)

# 0x01 2PC

Two-Phase Commit Protocol（两阶段提交协议），注意 ，2PC 是一个协议。

2PC 是一个强一致、中心化的原子提交协议。这里面包含一个中心化的协调者节点（coordinator）。

Example：订单服务 A，需要调用支付服务 B 去支付，支付成功则处理购物订单为待发货状态，否则就需要将购物订单处理为失败状态。

## 两个阶段：

#### Prepare 阶段

①、事务询问：Coordinator 收到请求以后，向所有参与者发送事务预处理请求，即 Prepare。开始等待参与者响应

②、执行本地事务：各参与者执行本地事务，但是不会真正提交，而是先向 Coordinator 报告自己的情况

③、返回事务询问响应：参与者根据本地事务的情况返回 Yes or No

#### Commit 阶段

④、Coordinator 统计参与者的返回值，返送 commit（参与者全回复 Yes） 或者 rollback（有参与者回复 No）

⑤、执行本地 commit 或者 rollbak

```flow
                      |
                      | request
                      v
    +-----------------+-----------------+
    |            Coordinator            |
    +-+------+----+-------+---+------+--+
      |      ^    |       |   ^      |
      |      |    |       |   |      |
    4 |    3 |    | 1   1 |   | 3    | 4
      |      |    |       |   |      |
      v      |    v       v   |      v
+-----+------+----+-+    ++---+------+-------+
|      Server A     |    |      Server B     |
+------+-----+------+    +---+-----+---------+
       |     |               |     |
     5 |     | 2           2 |     | 5
       |     |               |     |
       v     v               v     v
    +--+-----+---+         +-+-----+----+
    |     DB 1   |         |   DB 2     |
    +------------+         +------------+
```

## 缺点：
+ 性能差：2PC 是一个阻塞协议，即节点在等待休息的时候会阻塞
+ 单节点故障：Coordinator 作为一个中心化，如果单节点并且故障，整个分布式系统就会崩掉

# 0x02 3PC

3PC 的诞生主要是为了解决 2PC 出现的阻塞问题。与 2PC 相比提出了两个改动：
+ Coordinator 和参与者都引入超时机制
+ 在 2PC 的 Prepare 和 Commit 之前加入一个 check 阶段，以保证最后 Commit 阶段之前各参与节点状态一致

## 三个阶段：

#### CanCommit 阶段

①、锁询问：请求到达 Coordinator 后， Coordinator 发起 CanCommit 请求，各参与节点根据情况判断是否可以获取数据库锁

②、锁请求：各参与节点尝试获取本地数据库锁

③、锁返回：参与节点根据获取锁的情况返回 Yes or No

#### PreCommit 阶段

+ 如果所有参与节点都返回 Yes，则进入PreCommit 阶段，PreCommit 类似于 2PC 的Prepare 阶段，只是增加了超时机制，这里不再赘述

#### DoCommit 阶段

+ DoCommit 阶段类似于 2PC 的 Commit 阶段

```flow
                             |
                             | request
                             v
+----------------------------+------------------------+
|                       Coordinator                   |
+------+---+----+----+---+-------+---+----+----+---+--+
       |   ^    |    ^   |       |   ^    |    ^   |
       |   |    |    |   |       |   |    |    |   |
     7 | 6 |  4 |  3 |   | 1   1 |   | 3  | 4  | 6 | 7
       |   |    |    |   |       |   |    |    |   |
       v   |    v    |   v       v   |    v    |   v
+------+---+----+----+---+-+   +-+---+----+----+---+-----+
|         Server A         |   |         Server B        |
+---+---------+--------+---+   +---+---------+-------+---+
    |         |        |           |         |       |
  8 |       5 |        | 2       2 |         | 5     | 8
    |         |        |           |         |       |
    v         v        v           v         v       v
+---+---------+--------+-+       +-+---------+-------+-+
|           DB 1         |       |         DB 2        |
+------------------------+       +---------------------+
```

---
参考：

:pushpin: [分布式事务(1)---2PC和3PC原理](https://www.cnblogs.com/qdhxhz/p/11167025.html)

:pushpin: [CAP Theorem](https://devopedia.org/cap-theorem)

:pushpin: [谈谈分布式系统的CAP理论](https://zhuanlan.zhihu.com/p/33999708)

:pushpin: [The Two-Phase Commit Protocol](http://courses.cs.vt.edu/~cs5204/fall00/distributedDBMS/duckett/tpcp.html)