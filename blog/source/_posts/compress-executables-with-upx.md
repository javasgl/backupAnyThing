---
title: 使用upx压缩可执行文件
date: 2017-08-19 22:39:28
tags: [upx,compress,golang]
categories: [tools]
---

记得当初刚接触学习 golang 的时候,当时使用还是 go 1.4版本,和其他语言的入门一样，写了一个简单的 hello world 程序。
```
package main

import "fmt"

func main() {
	fmt.Println("Hello World!")
}

```
程序非常简单，整个源文件大小才 4KB。然后执行 go build 编译成可执行文件，结果让我非常惊讶的是这段简单的代码生成的可执行文件竟然有4M之多。
<!--more-->
当然，只有升级到了1.8版本之后，情况稍微好了一些，但是生成的可执行程序依然有1.6M之多。
```
du -sh hello*
1.6M	hello
4.0K	hello.go

```
虽然现在服务器的硬盘不再是限制，价格也比较低廉，但是网络带宽依然是瓶颈，当可执行文件需要通过网络进行分发的时候，文件的大小的影响就比较明显了。

这个问题可以通过 upx(the Ultimate Packer for eXecutables) 来解决。

地址：https://github.com/upx/upx

压缩效果非常明显，以下 helllo_1 和 hello_2 分别是默认压缩和使用 --brute 参数压缩之后的二进制文件：
```
upx -o hello_1 hello

upx --brute hello_2 hello

du -sh hello*

1.6M	hello
4.0K	hello.go
576K	hello_1
456K	hello_2

```
可见，压缩比相当可观，大概压缩了 70% 的大小。

下面是一个正式项目的压缩效果：
```
 14M	gomessage_v2
4.2M	gomessage_v2.compressed			//upx -o gomessage_v2.compressed gomessage_v2
4.1M	gomessage_v2.compressed.best		//upx --best -o gomessage_v2.compressed.best gomessage_v2
3.1M	gomessage_v2.compressed.brute		//upx --brute -o gomessage_v2.compressed.brute gomessage_v2
```
可见压缩之后可执行文件还是小了很多，从 14M 降低到了 3~4M 左右。
不同的参数代表着压缩程度，压缩的越多，压缩过程所需的时间也相对比较长。
