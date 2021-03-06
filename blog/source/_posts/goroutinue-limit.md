---
title: Buffered Channel + sync.WaitGroup 实现对 goroutinue 并发数的限制
date: 2017-09-18 09:17:34
tags: [go,goroutinue]
categories: [go]
---

写过爬虫的都应该考虑这个问题：不能毫无节制、毫无节操的无限制的并发爬取目标网站的内容，不然很容易被对方识别出来从而被封。

Go 的 goroutinue 机制使用起来非常简单，所以很多人比如我就喜欢用 Go 来写写爬虫。那么如何限制 goroutinue 并发数呢？

下面提供一种 Buffered Channel + sync.WaitGroup 实现对 goroutinue 并发数进行控制的思路。这种方式其实在 D&K 所著的《The Go Programing Language》一书中的第八章(goroutinue and channels) 中也有提到。

<!--more-->

具体代码如下：
```
package main

import (
	"fmt"
	"sync"
	"time"
)

func main() {

	wg := &sync.WaitGroup{}

	limiter := make(chan bool, 10)
	for i := 0; i < 100; i++ {
		wg.Add(1)
		limiter <- true
		go download(i, limiter, wg)
	}
	wg.Wait()
}

func download(index int, limiter chan bool, wg *sync.WaitGroup) {
	defer wg.Done()
	fmt.Println("start to download :", index)
	time.Sleep(1 * time.Second)
	<-limiter
}

```

这段代码中，sync.WaitGroup 主要用来管理 goroutinue ，而 limiter 这个带有缓冲的通道则是用来控制并发数。

由于通道的阻塞特性，当并发数达到规定的阈值(创建通道时指定的缓冲容量)之后, `limiter<-true`就会阻塞，其后面的 `go download(i,limiter,wg)`就不会被执行到，达到了暂停产生 goroutinue 的效果，实现了对 goroutinue 并发数的控制。

当其中一个 download goroutinue 完成的时候，从 limiter 中释放了一个空位，此时，被 limiter 所阻塞的 for 循环得以继续执行，从而继续产生新的 goroutinue。

