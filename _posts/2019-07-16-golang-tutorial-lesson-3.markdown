---
layout: post
title:  "Golang 系列讲座第三讲之变量"
date:   2019-07-16 17:13:25 +0800
author: hdu.yang
---

## 第三部分：变量

这是 `golang` 系列教程的第三讲，在这一讲中我们将讨论 `golang` 中的变量。

你可以通过阅读《Golang 系列教程第二讲：Hello World》来了解 `golang` 的配置以及运行 `hello world` 程序。

### 什么是变量

变量是在内存中存储一定类型的值的名字。`go` 中有多种不同的语法来申明变量。

### 申明单独变量

var name type 是申明一个单独变量的语法

```go
package main

import "fmt"

func main() {
    var age int // variable declaration
    fmt.Println("my age is", age)
}
```

`var age int` 申明了一个名为 `age` 的整型变量。我们并没有为它赋任何值，如果一个变量并没有赋值，`go` 会自动初始化一个该类型的零值给该变量。在这个例子中，`age` 被赋为数字 0。如果你运行上述程序，你可以看到终端会输出 “my age is 0”。

一个变量可以指定该类型的任何值。在上述程序中，`age` 可以被赋任何值。

```go
package main

import "fmt"

func main() {
    var age int // variable declaration
    fmt.Println("my age is ", age)
    age = 29 //assignment
    fmt.Println("my age is", age)
    age = 54 //assignment
    fmt.Println("my new age is", age)
}
```

上述程序会输出

my age is  0
my age is 29
my new age is 54

### 带初始值申明变量

一个变量在申明的同时可以赋初值。

`var name type = initialvalue` 是申明变量并初始化的语法。

```go
package main

import "fmt"

func main() {
    var age int = 29 // variable declaration with initial value

    fmt.Println("my age is", age)
}
```

在上面这个程序中，`age` 是类型为 `int` 的变量并含有初值 29。如果你执行上面的程序，你可以看到下面的输出，它表示变量 `age` 已经被初始化为 29 这个值。

my age is 29

### 类型推导

如果一个变量拥有初始值，那么 `Go` 会利用该初始值自动推断出该变量的类型。因此，如果一个变量拥有初始值，变量申明中的类型标识可以省略。

如果一个变量在申明时用的语法是 `var name = initialvalue`, `Go` 会通过该初始值自动推断出那个变量的类型。

在以下的例子中，我们可以看到变量 `age` 的类型申明 `int` 已经移除了，那是因为这个变量拥有值为 29 的初始值，`go` 可以推断中该变量的类型为 `int`。

```go
package main

import "fmt"

func main() {
    var age = 29 // type will be inferred

    fmt.Println("my age is", age)
}
```

### 多变量申明

多个变量可以在一个语句中同时申明。

`var name1, name2 type = initialvalue1, initialvalue2` 是多变量申明的语法。

```go
package main

import "fmt"

func main() {
    var width, height int = 100, 50 //declaring multiple variables

    fmt.Println("width is", width, "height is", height)
}
```

同样，当变量拥有初始值的时候类型可以省略，下面的程序通过类型推断申明多个变量。

```go
package main

import "fmt"

func main() {
    var width, height = 100, 50 //"int" is dropped

    fmt.Println("width is", width, "height is", height)
}
```

以上的程序会将 width is 100 height is 50 做为输出打印在屏幕上。

现在你大可以猜测当 width 和 height 没有初始值时，它们的初始值会被赋予 0。

```go
package main

import "fmt"

func main() {
    var width, height int
    fmt.Println("width is", width, "height is", height)
    width = 100
    height = 50
    fmt.Println("new width is", width, "new height is ", height)
}
```

以上程序会打印
width is 0 height is 0
new width is 100 new height is  50

有时候我们也需要申明不同类型的变量，下面的语法可以用来申明不同类型的变量

```go
var (
      name1 = initialvalue1
      name2 = initialvalue2
)
```

下面的程序使用上述的申明语法申明不同类型的变量

```go
package main

import "fmt"

func main() {
    var (
        name   = "naveen"
        age    = 29
        height int
    )
    fmt.Println("my name is", name, ", age is", age, "and height is", height)
}
```

这里我们申明了字符串类型的 name，整型的 age 和 height（我们将在接下来的课程中讨论 golang 可以使用的多种变量类型），运行上面的程序会打印出 my name is naveen , age is 29 and height is 0。

### 短路径申明

Go 也提供另一种简洁的方式申明变量。这就是大家都熟悉的短路径申明 `users := operator`

```go
package main

import "fmt"

func main() {
    name, age := "naveen", 29 //short hand declaration

    fmt.Println("my name is", name, "age is", age)
}
```

如果你运行上面的程序会看到输出 my name is naveen age is 29。

短路径申明需要左边的每个变量都拥有一个初始值，如果一个变量没有初始值，就会报错，下面的程序就会报错因为 age 变量没有被赋初值。

```go
package main

import "fmt"

func main() {
    name, age := "naveen" //error

    fmt.Println("my name is", name, "age is", age)
}
```

另外，只要左边的其中一个变量时新变量就可以使用短路径语法进行赋值。

```go
package main

import "fmt"

func main() {
    a, b := 20, 30 // declare variables a and b
    fmt.Println("a is", a, "b is", b)
    b, c := 40, 50 // b is already declared but c is new
    fmt.Println("b is", b, "c is", c)
    b, c = 80, 90 // assign new values to already declared variables b and c
    fmt.Println("changed b is", b, "c is", c)
}
```

考虑下面的程序在第8行变量 b 已经申明过了，而变量 c 却是第一次申明，因此该程序可以工作并输出
a is 20 b is 30
b is 40 c is 50
changed b is 80 c is 90

如果我们运行

```go
package main

import "fmt"

func main() {
    a, b := 20, 30 //a and b declared
    fmt.Println("a is", a, "b is", b)
    a, b := 40, 50 //error, no new variables
}
```

程序将报错 “没有发现新的变量在 := 赋值语句左边”，那是因为 a 和 b 都已经被赋值过了。

变量也可以在运行时被赋值。

```go
package main

import (
    "fmt"
    "math"
)

func main() {
    a, b := 145.8, 543.8
    c := math.Min(a, b)
    fmt.Println("minimum value is ", c)
}
```

上面程序中 c 的值在运行时才会被计算出，因此该程序将输出

minimum value is  145.8

既然 Go 是强制静态类型申明的语言，那么如果一个变量一开始属于某一种类型，那么它不能再被赋予其他类型。下面的程序会抛出 “cannot use "naveen" (type string) as type int in assignment” 因为 age 一开始是申明为 int 类型的，然后我们又将它指定为 string 类型。

```go
package main

func main() {
    age := 29      // age is int
    age = "naveen" // error since we are trying to assign a string to a variable of type int
}
```

谢谢阅读，请你们给我反馈并在评论区域查询问题。
