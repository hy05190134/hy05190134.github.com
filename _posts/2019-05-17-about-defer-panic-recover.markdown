---
layout: post
title:  "关于 Go 的 Defer，Panic 和 Recover"
date:   2019-05-17 11:13:25 +0800
author: hdu.yang
---

## 原文链接
[http://blog.golang.org/defer-panic-and-recover](http://blog.golang.org/defer-panic-and-recover)

## Defer
defer 语句意味着将这一部分的调用压入一个调用堆栈中，在 defer 所在的函数返回后从调用堆栈中一个个弹出并执行。defer 的出现可以让程序员想到在创建资源后该资源最终是需要回收的，而不必关心具体在哪里返回的时候回收。

defer 的使用需要遵循以下三条规则：

* defer 函数的参数在该 defer 语句被压入堆栈时被赋值，因此下面这段代码 defer 语句打印的值是 0

```
func a() {
    i := 0
    defer fmt.Println(i)
    i++
    return
}
```
* defer 语句执行的是先进后出的顺序，因此下面这段代码 defer 语句打印的值是 3210

```
func b() {
    for i := 0; i < 4; i++ {
        defer fmt.Print(i)
    }
}
```
* defer 函数可能会读取它所在的函数的返回值作为参数，因此下面这段代码中打印的返回值为 2

```
func c() (i int) {
    defer func() { i++ }()
    return 1
}
```

## Panic
panic 是 go 的内建函数，表示函数执行到某处后停止往下执行，但会执行完 defer 相关的逻辑，并打印协程相关的堆栈信息。函数具体执行过程中一般会因为运行时错误或者数组越界等错误而进行 panic 操作。

## Recover
Recover 也是 go 的内建函数，表示从 panic 的协程中恢复，并继续执行。Recover 只能用于 defer 的函数中，正常情况下 Recover 获取的值为空，因此不会对程序产生影响。如果当前的协程正在进行 panic，那么 Recover 会获取 panic 的值，并恢复到正常的执行逻辑上。

具体可以举个例子来理解下 panic 与 recover 的含义

```
package main

import "fmt"

func main() {
    f()
    fmt.Println("Returned normally from f.")
}

func f() {
    defer func() {
        if r := recover(); r != nil {
            fmt.Println("Recovered in f", r)
        }
    }()
    fmt.Println("Calling g.")
    g(0)
    fmt.Println("Returned normally from g.")
}

func g(i int) {
    if i > 3 {
        fmt.Println("Panicking!")
        panic(fmt.Sprintf("%v", i))
    }
    defer fmt.Println("Defer in g", i)
    fmt.Println("Printing in g", i)
    g(i + 1)
}
```
该函数的调用结果如下：

```
Calling g.
Printing in g 0
Printing in g 1
Printing in g 2
Printing in g 3
Panicking!
Defer in g 3
Defer in g 2
Defer in g 1
Defer in g 0
Recovered in f 4
Returned normally from f.
```

如果在 f 函数中不进行 Recover 调用，那么函数的结果如下：

```
Calling g.
Printing in g 0
Printing in g 1
Printing in g 2
Printing in g 3
Panicking!
Defer in g 3
Defer in g 2
Defer in g 1
Defer in g 0
panic: 4
 
panic PC=0x2a9cd8
[stack trace omitted]
```
## 总结
在用 go 的标准 json 库来解析 json 串时就用到了 go 的 panic 以及 recover 调用，另外 defer 还可以用来释放互斥锁，打印尾部等操作。总之，defer, panic 以及 recover 都是 go 里面对于正常流进行控制的一种机制，单独或者配合的使用可以实现其他语言中的一些特有功能。


