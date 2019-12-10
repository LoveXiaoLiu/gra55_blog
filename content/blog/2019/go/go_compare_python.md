+++
title = "Python 与 Golang 语法特性对比"
description = "Python 程序员入门 Golang，通过表格对比，加强记忆，也可以作为一个语法速查表"
author = "gra55"
categories = ["Go"]
tags = ["python", "go", "2019"]
date = "2019-12-10"
featured = "kobe_first_champ.jpg"
featuredalt = "Jerry Buss is interviewed after his team won the 1999-2000 NBA Finals"
featuredpath = "date"
linktitle = ""
type = "post"
+++

## 0x00 缘起

之前整体把 golang 的语法过了一遍，花了大概几个小时。

然鹅，2 天没写 go 代码，语法就忘完了，还得回过头去查一次，真是浪费时间。

所以，我就想到了这个联想记忆，通过熟悉的东西，来记住陌生的东西。

## 0x01 语法特性对比

> 参考：[go 语言之旅](https://tour.go-zh.org/welcome/1)


|  | Python | Go |
| :--------: | :-------- | :-------- |
| 包声明 | 文件名 | 文件内定义：package main |
| 引入包 | import json | import "math/rand" |
| 模块对象暴露 | \_\_all\_\_ | 包内对象首字母大写 |
| 函数 | def 关键字 | func 关键字 |
| 函数多指返回 | return x, y | return x, y（特例：func split(sum int) (x, y int) { x = sum * 4 / 9; y = sum - x; return }） |
| 变量 | 动态类型 | 关键字 var，在变量名后声明（多个变量类型相同时，前边类型可以忽略） |
| 变量初始化 | 直接赋值 | 如果提供初始值，则不需要指明类型 |
| 短变量声明 | / | :=（函数外必须使用 var 关键字） |
| 基本类型 | int、string、bool、list、dict、tuple | bool、string、int[8, 16, 32, 64]、uint[8, 16, 32, 64]、uintptr、byte（uint8 的别名）、rune（int32 的别名；表示一个 Unicode 码点）、float32、float64、complex64、complex128 |
| 零值 | / | 没有赋初值的变量默认为零值（int 为 0；bool 为 false；字符串为 ''；指针为 nil；切片为 nil，它的长度和容量为 0，且没有底层数组；映射为 nil，nil 映射既没有键，也不能添加键；） |
| 类型转换 | 同 go 语言 | T(v) 将值 v 转换为类型 T（不同类型直接赋值需要显式转换） |
| 常量 | 与变量相同 | 关键字 const，不能使用 := 语法（常量不指明类型的话，会根据上下文自动识别其类型） |
| for | for i in [1, 2, 3]: | for i:=0; i<10; i++ {xxx}（初始化语句和后置语句是可选的） |
| while | while True: | for sum < 1000 {xxx} |
| 死循环 | while True: | for {xxx} |
| if | if i < 10: xxx else: xxx | if v := math.Pow(x, n); v < lim {xxx} else {xxx}（if 和 for 语句类似，可以再 if 关键字后加一个初始化语句，这个变量的作用域是 if 和 else 作用域内） |
| switch | 无，使用字典代替 | switch os := runtime.GOOS; os {case "abc": xxx} （运行 switch 关键字后的赋值语句，然后运行匹配成功的那个 case，case 关键字后的值，无需为常量，也可以是函数等）（switch {} 等于 switch true {}）|
| 推迟调用 | 无 | 关键字 defer，作用是将指定的调用推迟到外层函数返回以后执行（defer 的调用会被压入栈中，所以先声明的后调用） |
| 指针 | 无，变量名都是引用 | 声明指针变量时，给类型前加 *；& 可以获取变量的地址；*p 可以通过指针设置或读取值 （var p *int，p = &i）|
| 结构体 | 类 | type Vertex struct {X int; Y int} v2 = Vertex{X: 1}（一组字段的集合，字段通过点号来访问，也可以通过指针来访问，指针前不需要加 *，编译器自己会处理优化） |
| 数组 | list | [n]T 表示拥有 n 个 T 类型的值的数组 |
| 切片 | [x:y]（生成一个新的数组） | 与 Python 类似的语法，可以忽略 x 和 y 的值，同样也是左闭右开的取值范围（切片生成的变量只是之前数组的引用）（切片有长度和容量：长度是包含的元素个数，容量是从第一个元素到底层数组的最后一个元素的个数。切片时左范围会改变数组容量）（可以使用 make 函数动态创建一个数组的切片：b := make([]int, len, cap)） |
| 数组添加元素 | append 方法 | append 函数：func append(s []T, vs ...T) []T。s 为切片，将新的值添加到切片最后，如果 s 的底层数组太小，会新分配一个更大的数组，然后返回的切片指向这个新数组 |
| range | 返回一个数组 | for i, v := range pow {xxx}，使用在 for 循环中，i 是当前元素在数组中的下标，v 是该下标对应的元素副本（这里副本是指针还是什么，有待查询？--副本指的是另外一个值一样，但是内存地址不一样的变量）（可以使用 _ 来忽略某一个变量，也可以手动忽略最后有一个变量。感觉有点像 js-es6 的语法） |
| 映射 | dict | 关键字 map，make 函数用来创建给定类型的映射并初始化：m = make(map[string]int)。（修改和获取键对应的值得方式和 Python 一样，删除使用 delete(m, key)，检测某个键是否存在 elem, ok = m[key]，ok 标识是否存在，若不存在 elem 是该映射元素类型的零值） |
| 函数也是值 | 是 | 是的，和 Python 一样，可以作为函数参数进行传递或者作为返回值 |
| 闭包 | 内嵌函数引用外部函数的变量，并且外部函数返回该内部函数 | 和 Python 闭包概念一样 |
| 方法 | 类中的方法 | 为结构体定义的函数叫做方法。方法是一类带有特殊**接受者**（就像 Python 中的 self）参数的函数：func (v Vertex) Abs() float64 {xxx}。当使用 Vertex 结构体的实例变量调用 Abs 方法（a := Vertex{3, 4}; a.Abs()）的时候，Abs 方法中的 v 就等于这里的 a 变量。（结构体的类型定义和方法声明必须在同一包内；不能为内建类型声明方法）（方法的接受者也可以是该类型的指针，这种更常用，因为指针可以修改结构体的值）（函数与方法的小区别：函数的参数如果是指针类型，那么调用的时候必须传一个指针类型；但是方法不一样，即使方法需要一个指针接受者，但是 a.Abs() 这样调用也是可以的，其中 a 不是指针类型，这是因为 go 内部将其隐士修改为 (&a).Abs()，同样的接受者为值类型的话，使用值和指针调用都可以。总结一下：不管方法的接受者是指针还是值类型，它的调用者可以是指针和值中的任意一个，通常选择使用指针作为接受者，因为指针接受者可以改变结构体内部的值，另外在每次调用的时候不需要复制该值）|
| 接口 | 继承 | 接口是一种类型，一组方法**签名**的集合（可以理解为父类，父类定义了很多接口，子类（这里相当于结构体）必须实现所有接口，才能满足 is-a 的关系）。type Abser interface {Abs() float64}。接口也是值，也可以当做参数或者返回值，它的值是一个元组 (value:值, type:类型)。nil 值仍然可以调用接口方法。为初始化的 nil 接口值，调用方法会报运行时错误，因为不知道需要执行哪个方法。 |
| 类型判断 | type 函数 | s, ok := i.(string)，s 是底层值，ok 表示是否是 string 类型 |
| 协程 | yield、gevent | goroutine 是由 go 运行时管理的轻量级线程。语法：go f(x, y, z)。f、x、y、z 的求值发生在当前的 goroutine 中，f 的执行发生在新的 goroutine 中。（goroutine 在相同的地址空间运行，因此访问共享的内存时必须进行同步，可以使用 sync 包。） |
| 信道 | / | 带有类型的管道，使用 make 函数创建：ch := make(chan int, 100)，第二个参数是可选的，表示信道的缓冲区大小，缓冲区满了发送数据会阻塞，缓冲区为空接收数据会阻塞；从信道发送接收内容：ch <- v; v := <-ch。默认情况下，在另一端未准备好的情况下，另一端是阻塞的状态（隐式同步）。（发送者可以通过 close 函数来关闭一个信道，表示没有任何内容需要发送了，注意，只有发送者才能关闭信道，接受者不可以关闭。接收时 v, ok := <-ch，信道关闭后，ok 会被置为 false。）（for i := range c 可以不断从信道读取数据，直到信道关闭。信道和文件不同，通常情况下不用主动关闭它们，除非需要告诉接受者不会再给信道发送数据了，比如这里需要通过 rang 来终止循环。） |
| select | select：io 多路复用的 Windows 第三方库 | select 语句可以通过一个 goroutine 来等待多个信道通信操作，语法：select {case c <- a:xxx;  case <-quit:xxx; default:xxx}。（select 会一直阻塞，直到有分支可以继续执行，如果不想阻塞的话，可以定义 default 分支；如果多个分支都可以执行，会随机选择一个分支执行。） |
| 锁 | threading.Lock | sync.Mutex |