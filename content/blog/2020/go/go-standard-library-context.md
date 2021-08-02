+++
title = "Golang 标准库：context"
description = "context 包定义了 Context 类型，可以用来跨进程传输信息、帮助 API 传输数据，并且可以携带 deadlines 或者 cancellation 信号"
author = "gra55"
categories = ["Go"]
tags = ["go", "2020"]
date = "2020-11-27"
featured = "kobe-04-as-gty.jpg"
# figurebedurl = "https://wx3.sinaimg.cn/large/8a6fa043ly1g9snihiopsj218i0u0qa8.jpg"
featuredalt = "Fog may have disoriented pilot in helicopter crash that killed Kobe Bryant, 8 others: NTSB"
featuredpath = "date"
linktitle = ""
type = "post"
+++

## 0x00 简介
Context 是以链式的方式来保存数据：context A 派生出了 context B，context B 派生出了 context C ......

当一个 context 被 canceled，所有从它派生的 context 都会被取消：B 被取消，C 同样也会被废弃

调用 `WithCancel`、`WithDeadline`、`WithTimeout` 这三个函数，入参是 `parent context`，出参是包含了 `parent context` 的 `child context` 和 CancelFunc函数。
+ 调用 CancelFunc 函数会废弃 `child context`，并且会废弃 `child context` 的 context，同样会切断与 `parent context` 的关联，也会暂停所有相关的定时器。
+ 如果没有调用 CancelFunc 函数，只能等到 `parent context` 被废弃或者定时器被触发。
+ go 的 vet 工具会检查所有的 context 链中 CancelFuncs 函数是否被调用。

## 0x01 最佳实践

+ 不要将 context 对象保存在结构体类型中。我们应该显式的传递 context 给需要的函数，而且 context 必须是第一个参数，通常命名为 ctx
+ 不要传递一个 nil 的 context，即使你不需要他。此时可以使用 context.TODO 代替
+ context 的 value 只建议保存进程和 APIs 的请求数据，不建议用来传递函数的可选参数
+ context 是协程安全的，多个 goroutine 可以操作相同的 context

## 0x02 常用函数
### WithCancel
> func WithCancel(parent Context) (ctx Context, cancel CancelFunc)

WithCancel 返回一个 parent context 的副本 child context，child context 同时携带了一个 Done channel，当返回的 CancelFunc 函数被调用时 context's Done channel 就会被关闭，此外如果 parent context's Done channel 被关闭，child context's Done channel 同样也会被关闭。

cancel context 意味着程序会释放掉与该 context 相关的所有资源，所以建议在操作完成后立即 cancel context

如下示例，使用 canceled context 可以防止 goroutine 泄漏，gen 函数得到 ctx.Done() 的信号以后会结束该函数。
```go
package main

import (
	"context"
	"fmt"
)

func main() {
	// gen generates integers in a separate goroutine and
	// sends them to the returned channel.
	// The callers of gen need to cancel the context once
	// they are done consuming generated integers not to leak
	// the internal goroutine started by gen.
	gen := func(ctx context.Context) <-chan int {
		dst := make(chan int)
		n := 1
		go func() {
			for {
				select {
				case <-ctx.Done():
					return // returning not to leak the goroutine
				case dst <- n:
					n++
				}
			}
		}()
		return dst
	}

	ctx, cancel := context.WithCancel(context.Background())
	defer cancel() // cancel when we are finished consuming integers

	for n := range gen(ctx) {
		fmt.Println(n)
		if n == 5 {
			break
		}
	}
}
```

### WithDeadline
> func WithDeadline(parent Context, d time.Time) (Context, CancelFunc)

WithDeadline 同样返回一个 child context，不过限制了 child context 的截止时间不超过 d，如果 parent context 的时间早于 child，那么截止时间统一等于较早时间。当截止时间到达或者 CancelFunc 函数被调用又或者 parent context's Done channel 被关闭，都会导致 child context's Done channel 被关闭。

```go
package main

import (
	"context"
	"fmt"
	"time"
)

const shortDuration = 1 * time.Millisecond

func main() {
	d := time.Now().Add(shortDuration)
	ctx, cancel := context.WithDeadline(context.Background(), d)

	// Even though ctx will be expired, it is good practice to call its
	// cancellation function in any case. Failure to do so may keep the
	// context and its parent alive longer than necessary.
	defer cancel()

	select {
	case <-time.After(1 * time.Second):
		fmt.Println("overslept")
	case <-ctx.Done():
		fmt.Println(ctx.Err())
	}

}
```

### WithTimeout
> func WithTimeout(parent Context, timeout time.Duration) (Context, CancelFunc)

相当于是 WithDeadline(parent, time.Now().Add(timeout))

### CancelFunc
> type CancelFunc func()

CancelFunc 作用是取消一项任务，不需要等待这个任务执行完成。CancelFunc 可能被多个 goroutine 同时调用，第一次调用以后，后边的调用没有任何效果。

### Context 接口
```go
type Context interface {
    Deadline() (deadline time.Time, ok bool)    // 返回截止时间
    Done() <-chan struct{}    // 返回一个被关闭的 channel，如果 context 永远不会被 canceled，则返回 nil。
    Err() error    // 如果 Done 没有被关闭，返回 nil
    Value(key interface{}) interface{}    // 返回对应 key 的 val
}
```

### Background 方法
> func Background() Context

Background 方法返回一个非 nil 的空 Context 对象。这个 Context 永远不会被 canceled，而且没有 deadline。

这个一般用在主函数、初始化函数等场景，作为最顶层 Context 存在。

### TODO 方法
> func TODO() Context

TODO 也返回一个非 nil 的空 Context 对象。使用场景：当前不清楚使用哪个 Context、当前 Context 参数不可用，以后再扩展。

### WithValue 方法
> func WithValue(parent Context, key, val interface{}) Context

WithValue 返回一个 parent context 的副本，这个副本包含了 key-val 键值对。key 必须是可比较的，为了避免冲突并且不应该是 string 或者其他内置类型，WithValue 的使用者建议自定义一种类型作为 key 来使用， 不能是 interface 类型，必须是 struct 类型。Alternatively, exported context key variables' static type should be a pointer or interface.

```go
package main

import (
	"context"
	"fmt"
)

func main() {
	type favContextKey string

	f := func(ctx context.Context, k favContextKey) {
		if v := ctx.Value(k); v != nil {
			fmt.Println("found value:", v)
			return
		}
		fmt.Println("key not found:", k)
	}

	k := favContextKey("language")
	ctx := context.WithValue(context.Background(), k, "Go")

	f(ctx, k)
	f(ctx, favContextKey("color"))

}
```

---
参考：

:pushpin: [context 官方文档](https://golang.org/pkg/context/)
