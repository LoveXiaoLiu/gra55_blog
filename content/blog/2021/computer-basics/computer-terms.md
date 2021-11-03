+++
title = "计算机术语【持续更新】"
description = "收集计算机相关的术语大全"
author = "gra55"
date = "2021-10-24"
categories = ["计算机基础"]
tags = ["基础", "2021", "术语"]
+++

|原文|翻译|解释|
|--|--|--|
|endpoint|uri|Web服务中|
|stub||rpc中使用，是一段代码，用来转换客户端与服务器之间传递的参数|
|prefork||多进程模式，pre 表示在请求来之前子进程已经创建好了（进程池？），一般用在非线程安全的情况下|
|用户认证||判断是否是合法用户|
|用户权限||查看合法用户有没有权限|
|ELF|可执行连接格式（Executable and Linkable Format）|是UNIX系统实验室（USL）作为应用程序二进制接口（Application Binary Interface，ABI）而开发和发布的。扩展名为elf。|
|GNU|GNU's not unix|GNU 是追求开源一项运动。Unix 系统被发明以后，大家用的很爽，但是后来闭源开始收费了。一个叫 RMS 的大叔觉得很不爽，于是发起 GNU 计划，模仿 Unix 的界面和使用方式，从头做一个开源的版本。然后他自己做了编辑器 Emacs 和编译器 GCC。做了很多可以运行在 Unix 上的开源软件，但是一直没做出操作系统，于是一个叫 Linus 的博士，写出来 Linux 操作系统，完美符合 GNU 的目的，所以最后 Linux 也纳入了 GNU 中。|
|AT&T syntax||AT&T 是 GNU 汇编语言使用的语法格式，与之相关的是 NASM 汇编语言使用的 Intel 风格的语法|
|RAM|随机储存器（Random-Access Memory）|断电后数据丢失，也就是电脑上的内存条，程序时放在 RAM 中运行的|
|ROM|只读储存器（Read-Only Memory）|断电数据不丢失，通俗来讲就是电脑上的硬盘|
|IA-32|Intel Architecture, 32-bit 的缩写|有时候也叫做 i386|
|CGI|[Common Gateway Interface](https://en.wikipedia.org/wiki/Common_Gateway_Interface)|通用网关接口。早期的 web 服务，后端会绑定一个目录（`/usr/local/apache/htdocs/`），浏览器请求时，会直接返回目录下 index.html 文件，这就是静态请求，无法满足后来动态请求的发展趋势，所以当浏览器请求 web 服务的特定 URL 时，不直接返回 index.html，而是执行一个脚本，这个脚本的输出会作为 http response 返回给浏览器端，这个脚本就叫做 CGI 脚本。CGI 脚本在执行的时候，还会通过环境变量等形式获取到 http request 带过来的附加信息。慢慢的，CGI 泛指后端服务。|