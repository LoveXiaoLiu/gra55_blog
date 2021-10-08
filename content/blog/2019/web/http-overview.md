+++
title = "HTTP 协议透彻解析"
description = "从入门到放弃的讲解 HTTP 协议"
author = "gra55"
categories = ["Web"]
tags = ["web", "http", "2019"]
date = "2019-07-18"
[[images]]
  src = "img/2019/07/zgr-bwbj.jpeg"
  alt = "张国荣在《霸王别姬》中的幕后照"
  stretch = "horizontal"
+++

> keywords:
> + http 协议是什么（历史、未来发展）
> + http 协议的传输过程
> + http 协议包内容
> + http 协议的问题

## 0x00 HTTP 协议是什么

> btw：什么是协议？
> 协议不光在计算机中使用，我们平时生活中经常会用到协议。比如说两个网友奔现，双方都不认识，那就必须定义一个约定或者协议：男方来了必须穿黑色牛仔裤，手里拿一束花，女方必须穿白色上衣，背红色包包。这样，才能互相认识，否则就无法完成奔现。

先参照上面的例子简单说下 HTTP 协议：女方就好比是 Client 端，男方就好比是 Server 端。Client 和 Server 进行通信的时候双方不认识，那就必须遵守某种协议才能进行交互。这个协议就是 HTTP 协议。

接下来用正式的、官方的解释说下 HTTP 协议：HTTP 协议是一种通过 Web 获取资源（HTML、CSS、文本等）的协议。它是数据交互的基础，是一种 C-S 结构的协议。

官方的解释很清楚，**HTTP 协议的作用就是在网络上获取资源**。

Key point：
+ HTTP 交互的是单独的 message，而不是数据流（stream of data）
+ HTTP 是应用层协议，通过 TCP 或者 TLS 加密的 TCP 连接发送数据
+ HTTP 协议的高度可扩展性，使它能传输任何类型的文件（video、imag 等）
+ Client 与 Server 中间可以有多层代理
+ HTTP 协议依靠 HTTP headers 可以自由的扩展功能
+ HTTP 协议是无状态的（stateless，同一个连接先后发送的两个请求是没有联系的），但是是有会话状态的（not sessionless，通过 HTTP header 的可扩展性，HTTP cookie 支持有状态的会话）

#### 历史

早在 1990 年，HTTP 协议就被设计出来了，它是一个高度可扩展的协议，灵活性高，所以发展的很迅速。

origin constraint 在 2010 年被放宽限制，通过指定 specific header 可以跨域访问。

HTTP/1.0
+ 一个 HTTP 请求建立一个 TCP 连接

HTTP/1.1
+ 增加了持久连接（persistent connections），使用 HTTP Connection header 来控制 TCP 连接
+ pipelining，这个很难实现（**现在有没有实现待确认？**）

HTTP/2 
+ 将 HTTP 消息封装到 frame 中
+ 传输方式更加优化，使用[多路复用技术（multiplexing）](https://stackoverflow.com/questions/36517829/what-does-multiplexing-mean-in-http-2)，即一条连接发送多个 HTTP 请求。与 HTTP/1.1 主要不同就是可以在同一条 TCP 连接上发送/接收多个请求，而不需要等待上一个请求返回，HTTP/1.1 是顺序请求的。

#### 未来

+ 针对 transport protocol 在做一些实验：Google 正在做 [QUIC](https://en.wikipedia.org/wiki/QUIC)

---
参考：

:pushpin: [HTTP BY MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP)

