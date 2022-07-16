+++
title = "GDB 调试 Python"
description = "介绍如何使用 gdb 来调试 Python 程序"
author = "gra55"
categories = ["Python"]
tags = ["python", "2022", "基础"]
date = "2022-07-16"
+++

## 1. 背景

很多问题在 Python 中很难调试，比如说：

1. 段错误（segfaults），这种错误不是未捕获的 Python 异常，而是程序真正发生的段错误
2. 进程 hung 住（hung processes），这种情况下无法获取堆栈信息，也无法通过 pdb 进行调试
3. 守护进程失控（out of control daemon processes）

出现以上 3 种情况，可以使用 gdb 进行调试。

## 2. 依赖

使用 gdb 调试 Python 时，需要具体以下条件：

1. 安装好 gdb
2. 安装调试 Python 的扩展包，扩展包包含了：
   1. debugging symbols
   2. Python 特定的 gdb 命令

各个系统安装 gdb 和 python-debuginfo 的方式：

+ Fedora
  * sudo yum install gdb python-debuginfo
+ Ubuntu
  * sudo apt-get install gdb python2.7-dbg
+ Centos*
  * sudo yum install yum-utils
  * sudo debuginfo-install glibc
  * sudo yum install gdb python-debuginfo

## 3. 开始调试

### 3.1 调试已有进程

命令：gdb python `$pid`。

这种情况，会使运行中的进程停止，使用 c 命令可以使其继续运行

### 3.2 gdb 启动 Python 进程

命令：

+ gdb -ex r --args python `<programname>`.py `<arguments>`
+ gdb python; run `<programname>`.py `<arguments>`

这种情况下，程序会一直运行，直到退出、segfaults 或者 Ctrl+C

### 3.3 调试-段错误

如果被调试的程序有段错误（segfaulted），gdb 会自动暂停程序，此刻，就可以使用 Python 的 gdb 命令来查看相关信息。

可以执行如下命令：

1. bt：获取 C 的堆栈。如果这里查不到可用信息，继续往下执行；
2. py-bt：获取 Python 堆栈。

### 3.4 调试-进程 Hung

如果一个进程出现挂起，它要么在等待某个东西（锁、IO等），要么在某个繁忙的循环中。这两种情况下，我们可以使用 gdb attach 到进程，查看堆栈信息。

如果进程处于繁忙循环中，我们可以继续往下执行代码（使用 cont 命令），然后再次中断（Ctrl+C），然后就可以查看堆栈信息了。

如果 hung 发生在某个线程，可以执行如下操作：

1. 使用 info threads 命令查看所有线程，带星号的线程为当前正在执行的线程
2. py-list 查看当前线程执行到的代码位置
3. thread apply all py-list（简写：t a a py-list）查看所有线程的代码位置
4. frame X 可以用来切换栈帧

## 参考：

📌 [https://wiki.python.org/moin/DebuggingWithGdb](https://wiki.python.org/moin/DebuggingWithGdb)

📌 [https://fedoraproject.org/wiki/Features/EasierPythonDebugging](https://fedoraproject.org/wiki/Features/EasierPythonDebugging)

📌 [https://github.com/spyder-ide/spyder/wiki/How-to-debug-Spyder-deadlock-freeze-hang](https://github.com/spyder-ide/spyder/wiki/How-to-debug-Spyder-deadlock-freeze-hang)

📌 [gdb 支持的 Python 命令](https://fedoraproject.org/wiki/Features/EasierPythonDebugging#New_gdb_commands)
