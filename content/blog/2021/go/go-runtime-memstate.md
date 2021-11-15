+++
title = "GO Runtime 内存状态"
description = "Runtime 中的内存状态字段解析，pprof 中也会输出相关的内存字段供排查问题"
author = "gra55"
date = "2021-11-15"
categories = ["Go"]
tags = ["go", "2021"]
+++

> 官方文档：[https://pkg.go.dev/runtime#MemStats](https://pkg.go.dev/runtime#MemStats)

## 0x00 概述

![memstate](/img/2021/11/go-memstate.png)

Alloc uint64
+ 堆中分配的对象所占用的字节数，与 HeapAlloc 是一样的

TotalAlloc uint64
+ 堆中分配的对象所占用的字节数的累积值（释放内存这个数值不会减小）

Sys uint64
+ 从操作系统获得的内存总字节数，包含 go 运行时的堆、栈和其他数据结构，通常情况下这个值会保持不变

Lookups uint64
+ runtime 执行的指针查找次数

Mallocs uint64
+ 堆中分配的对象累积的数量，活跃对象计算公式：Mallocs - Frees

Frees uint64
+ 释放的堆对象的累积计数，活跃对象计算公式：Mallocs - Frees

HeapAlloc uint64
+ 堆中分配的对象所占用的字节数，与 Alloc 是一样的。（这里包含所有可达的对象和 GC 尚未释放的不可达对象）

HeapSys uint64
+ 从操作系统获得的堆内存字节数（包含预留的未使用的虚拟地址空间）

HeapIdle uint64
+ 堆中未使用的字节数（这些内存字节可以返回给 OS，也可以被堆或栈重复使用。HeapIdle-HeapReleased是可以返回给 OS 的内存，但是现在由 runtime 保留，这样就可以不从 OS 申请内存而来增加堆的大小。如果这个值很大，大于堆大小，则表明活跃的堆大小最近出现过瞬时峰值）

HeapInuse uint64
+ 在使用的堆内存（HeapInuse - HeapAlloc 专用于特定大小的类的内存，但是现在还没有使用。这是碎片的上限值，通常可以有效重用此内存）

HeapReleased uint64
+ 返回给操作系统的物理内存字节，尚未被堆重新获取的内存字节

HeapObjects uint64
+ 在堆中创建的对象数量（随对象的创建和释放而增减）

StackInuse uint64
+ 栈使用的字节数（没有StackIdle是因为，未使用的栈返回到堆中，因此计入 HeapIdle）

StackSys uint64
+ 从 OS 获取的栈内存字节数（StackSys = StackInuse + 任何为 OS 线程栈从 OS 申请的内存）

GCSys uint64
+ GC 元数据使用的字节数

NextGC uint64
+ 下一个 GC 周期的目标堆的大小（GC 的目标是保持HeapAlloc ≤ NextGC）

LastGC uint64
+ 最后一次垃圾收集完成的时间，纳秒的 Unix 时间戳

PauseTotalNs uint64
+ 自程序启动以来，GC stop-the-world 的累积纳秒数（在 stop-the-world 暂停期间，所有 goroutine 都会暂停，只有垃圾收集器可以运行。）

PauseNs [256]uint64
+ 最近的GC stop-the-world 持续的时间，纳秒为单位

PauseEnd [256]uint64
+ 最近 GC 暂停结束的纳秒时间戳

NumGC uint32
+ GC 循环的次数

NumForcedGC uint32
+ 由应用调用 GC 函数所执行的 GC 的次数

GCCPUFraction float64
+ GCs 使用的 CPU 的比例
