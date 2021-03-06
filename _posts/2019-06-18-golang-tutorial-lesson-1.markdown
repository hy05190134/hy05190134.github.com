---
layout: post
title:  "Golang 系列讲座第一讲之介绍与安装"
date:   2019-06-18 11:13:25 +0800
author: hdu.yang
---

# 第一部分：介绍与安装

这是我们 Golang 系列教程的第一课。在这节课中我们会简单介绍 Go 并且会讨论 Go 相比其他编程语言的优势。另外我们将学习如何在 Mac, Windows 和 Linux 上安装 Go。

## 介绍

Go 也叫 Golang 是一种由谷歌公司开发的开源的静态类型编译的编程语言。Go 创造背后的关键人物是 Rob Pike，Ken Thompson 以及 Robert Griesemer。Go 的第一个版本是在 2009 年 10 月份公开发布的。

Go 是一种通用的包含一系列标准库支持的语法简单的编程语言。Go 非常适合用于编写那些高可用弹性的网络应用。Go 也适用于创建命令行应用，桌面应用甚至于移动应用。

## Go 的优势

为何你会选择 Go 作为服务端的编程语言，尽管已经有像 python, ruby, nodejs 等大量的其他编程语言可以做同样的工作。

下面我将列出 Go 的几点优点：

### 简单的语法

Go 的语法是简单和简洁的，并且没有充斥着不必要的特征，这使得用 Go 写的代码可读性好并且方便维护。

### 方便编写并发程序

并发是 Go 语言内在的一部分，因此，编写多线程的并发程序只是小菜一碟。这是通过 Goroutine 和 Channel 这两个特性实现的，我们会在接下来的教程中具体讨论这两部分。

### 编译语言

Go 是一种静态编译的语言。Go 的源代码将会编译成原生的二进制，这个是在类似 nodejs 中使用的解释型语言 Javascript 所欠缺的。

### 快速编译

Go 编译器从一开始设计就强调了快速编译，并且事实证明它的编译速度是相当快的

### 静态链接

Go 编译器支持静态链接。所有的 Go 代码都可以静态链接成一个大的二进制，这个二进制可以部署到云服务上而不需要担心各种库的依赖问题。

### Go 自带工具

Go 自带的工具值得特别提到。Go 从一开始创建就包含了各种强大的工具，开发者可以利用这些工具编写出更好的代码。

* gofmt - gofmt 是一个针对源代码自动格式化的工具，它使用 tab 作为缩进，以及使用空白来对齐。
* vet - vet 分析 go 的源代码然后报告可能存在的可疑代码。vet 报告的问题并不一定是真实的问题，但是它能捕获一些编译器所不能发现的比如 Printf 格式不正确的问题。
* golint - golint 用来识别代码风格问题

### 垃圾回收

Go 在内存管理上使用垃圾自动回收机制，因此开发者不需要担心内存的管理，这也有助于开发者更方便的编写并发程序。

### 简单语法规则

Go 语言的语法规则是相当简单的。整个语法规则只占1整页，按照该语法规则说明，你甚至可以自己编写编译器。

### 开源

最后提一点，Go 是一个开放源码的项目。你也可以贡献你自己的源码并参与进来。

## 用 Go 编写的流行产品

以下流行的产品都是用 Go 开发的

* Google 用 Go 开发了 Kubernetes
* Docker - 世界上最流行的容器平台也是用 Go 开发的
* Dropbox 已经将影响性能的至关重要的组件从 Python 迁移到 Go 开发

## 安装

Go 支持 Mac，Linux 以及 Windows 操作系统。你可以从 https://golang.org/dll/ 下载相关操作系统的二进制安装包。

### Mac OS

从 https://golang.org/dll/ 下载安装包后双击开始安装，然后跟着提示安装完成。这个时候 Go 会被默认安装在 /usr/local/go 目录下，并且已经将 /usr/local/go/bin 加入到 PATH 环境变量中。

### Windows

从 https://golang.org/dll/ 下载安装包后双击开始安装，然后跟着提示安装完成。这个时候 Go 会被默认安装在 c:\Go 目录下，并且已经将 c:\Go\bin 加入到 PATH 环境变量中。

### Linux

从 https://golang.org/dll/ 下载安装包后双击开始安装，然后解压到 /usr/local 目录下。这个时候 Go 会被安装在 /usr/local/go 目录下，需要自己将 /usr/local/go/bin 加入到 PATH 环境变量中。

在下一课 [Golang 系列教程2: Hello World](http://www.hy05190134.com/2019/06/19/golang-tutorial-lesson-2.html) 中，我们将创建工作目录并且编写我们第一个 Go 程序。请提供有效的反馈与评价，谢谢阅读该教程。
