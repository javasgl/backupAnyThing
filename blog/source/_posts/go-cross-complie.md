---
title: 交叉编译 go 程序
date: 2017-08-29 08:54:51
tags: [go,complie]
categories: [go]
---
相信大多数人和我一样，都是在 mac 或者 windows 上写 go 代码，但是最终上线运行却是在 Linux 系统之上。这就涉及到一个问题：由于操作系统架构的差异，编译过后的二进制程序能够在多个系统下正常运行么？

go 不像 java 等运行于 JVM 之上的语言，也不像 php , python 等解释性语言，它需要编译后不依赖于其他环境就能执行，但是编译过程却依赖于系统架构，所以 go 需要交叉编译。


释义：交叉编译，就是在一个平台上生成另一个平台上的可执行代码。

go 非常方便的支持交叉编译。
<!--more-->

我们先来看看不同环境下 go env 返回的结果。

Mac 下：
```
GOARCH="amd64"
GOOS="darwin"
CGO_ENABLED="1"
```
Linux 下：
```
GOARCH="amd64"
GOOS="linux"
CGO_ENABLED="1"
```
架构均是 x64 架构，区别是 GOOS 。

比如，我在 mac 上开发，最终需要部署到 linux 上运行，在没有 ci(持续集成)的支持下，一般有两种方式：
- 将源代码复制到目标服务器上( scp 或者 git )，然后在目标机器上 go build 编译。
- 本地编译，只将最终编译过后的二进制文件复制到目标机器上。

大多数肯定倾向于第二种方式，那么这时候就需要交叉编译了。

编译成Linux下可执行文件
```
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build
```
编译成Windows下可执行文件
```
CGO_ENABLED=0 GOOS=windows GOARCH=amd64 go build
```
编译过后，将二进制传送到相应系统即可运行。

但是，这种交叉编译有一点限制就是：

不支持CGO的交叉编译，如果程序中使用了 CGO , 则不能通过这种方式来交叉编译。

如果使用 GUN Make ,则可以将以上参数写到 Makefile 文件中:
```
build-linux:
	export CGO_ENABLED=0 && export GOOS=linux && export GOARCH=amd64 && go build

build-wins:
	export CGO_ENABLED=0 && export GOOS=windows && export GOARCH=amd64 && go build

```

参考：
 - [cross-compilling-go](https://solovyov.net/en/2012/cross-compiling-go/)
