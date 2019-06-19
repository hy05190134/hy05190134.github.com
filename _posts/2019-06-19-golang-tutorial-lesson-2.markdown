---
layout: post
title:  "Golang 系列讲座第二讲之Hello World"
date:   2019-06-19 11:13:25 +0800
author: hdu.yang
---

## 第二部分： Hello World

这是我们 `Golang` 系列教程的第二课。我们可以通过阅读《Golang 系列教程第一课：介绍和安装》了解什么是 `Golang` 以及如何安装 `Golang`。

对于学习一门编程语言来说，没有什么比手写代码来得更有效了。让我们直入主题来编写我们第一个 `Go` 程序吧。

我个人推荐使用带 `Go` 扩展插件的 vim 作为编辑器来写 `Go` 代码，因为它有自动填充，代码风格检查等一系列特点。

### 创建 Go 工作目录

在开始写代码之前，我们需要先创建工作目录。

在 Mac 或者 Linux 中，我们可以将 `$HOME/go` 目录作为 Go 工作目录，所以我们可以直接在 `$HOME` 下创建 go 目录文件。

在 Windows 中，我们可以创建 `C:\Users\YourName\go` 目录作为 Go 工作目录，所以我们可以直接在 `C:\Users\YourName` 下创建 go 目录文件。

我们也可以使用其他的目录作为 Go 工作目录，但前提是需要将该路径加入 `GOPATH` 环境变量。不过为了简便我们还是使用上面提到的路径。

所有的 `go` 的源文件都存放在工作目录下的 `src` 目录中。所以我们首先需要在 `go` 目录下面创建 src 目录。

每一个 `go` 项目也应该在 `src` 下面拥有属于自己的子目录。所以我们需要在 `src` 下面创建 `hello` 目录用来保存 `hello world` 这个项目。

在创建了上述目录后，目录结构看上去如下所示：

    go
        src
            hello

然后用下面的代码在 hello 目录下创建 helloworld.go

```go
package main

import "fmt"

func main() {  
    fmt.Println("Hello World")
}
```

创建好后的目录结构如下所示：

    go
        src
            hello
                helloworld.go

### 运行一个 Go 程序

事实上我们有多种方法可以运行一个 Go 程序。下面我们一个一个了解

    1）使用 go run 命令—— 在命令提示符下输入 go run workspacepath/src/hello/helloworld.go。
    上面提到的 workspacepath 应该用真正的工作目录路径代替(Windows 用 C:/Users/YourName/go 或者 Linux 下用 $HOME/go 代替)。
    你应该能看到屏幕上打印出 Hello World。
     
    2）使用 go install 命令        
     输入 go install hello workspacepath/bin/hello 执行，上面提到的 workspacepath 应该用真正的工作目录路径代替(Windows 用 C:/Users/YourName/go 或者 Linux 下用 $HOME/go 代替)，你也能看到屏幕上打印出 Hello World。
     当你输入 go install hello 时，go 的工具会搜索名为 hello 的包（hello 被称为包，我们将在后面深入了解 go 的包管理），此时 go 会在工作目录下创建 bin/hello 文件。此时的文件目录结构如下所示：
    go
        bin  
            hello
        src
            hello
                helloworld.go
     3）第三种很酷的运行程序的方法就是将程序放到 go 游乐场运行。虽然在游乐场运行的 go 有很多限制，但是对于运行简单的 go 程序是绰绰有余的。我已经给 hello 创建了一个游乐场，我们可以点击链接去在线运行。你也可以通过 go 游乐场与其他人分享你的代码。

### 对 hello world 程序的简单解释

以下是我们刚写的 hello world 程序

```go
package main //1

import "fmt" //2

func main() { //3  
    fmt.Println("Hello World") //4
}
```

我们将简单的了解到程序的每一行都做了什么。我们将在接下来的教程中深入程序的每一部分。

**package main** — 每个 go 文件必须以 package name 开头。使用包的好处在于使得代码能够独立和重复利用。这里的包名是 main。

**import "fmt"** — fmt 包被引用进来，我们可以在 main 函数中使用 fmt 包打印文本到标准输出

**func main()** — main 是一个特殊函数。程序都是从 main 函数开始执行的。main 函数总是存在 main package 中。左大括号和右大括号表示 main 函数的开始和结束。

**fmt.Println("Hello World")** — fmt 中的 Println 函数用来输出文本到标准输出

这部分代码可以在[github](https://github.com/golangbot/hello)的链接中找到。

现在你们可以继续到《Golang 教程第三讲：变量》中学习关于 golang 中的变量相关知识。

请提供有效的反馈并欢迎在评论区提问题，谢谢。
