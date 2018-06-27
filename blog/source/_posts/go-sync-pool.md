---
title: sync.Pool缓存池中对象回收时机
date: 2018-06-27 11:48:04
tags: [go,sync.Pool]
categories: go
---

为了缓解GC压力，go标准库在sync包中提供了一个Pool，但是这个Pool和我们一般意义上的Pool不太一样，主要有以下几点区别:
1.Pool无法设置大小，所以理论上只受限于系统内存大小。
2.Pool中的对象不支持自定义过期时间及策略，究其原因，Pool并不是一个Cache.
3.Pool的设计初衷是为了缓解GC压力，所以Pool中的对象会在GC开始前全部清除。

<!--more-->

下面这段注释来源于`pool.go`:
```
// A Pool is a set of temporary objects that may be individually saved and
// retrieved.
//
// Any item stored in the Pool may be removed automatically at any time without
// notification. If the Pool holds the only reference when this happens, the
// item might be deallocated.
//
// A Pool is safe for use by multiple goroutines simultaneously.
//
// Pool's purpose is to cache allocated but unused items for later reuse,
// relieving pressure on the garbage collector. That is, it makes it easy to
// build efficient, thread-safe free lists. However, it is not suitable for all
// free lists.
```
让我们用代码验证一下 sync.Pool中对象的回收时机:
```go
package main

import (
	"fmt"
	"runtime"
	"sync"
)

func main() {

	p := &sync.Pool{
		New: func() interface{} {
			return 0
		},
	}
	a := p.Get().(int)
	p.Put(1)
	b := p.Get().(int)
	fmt.Println(a, b)

	a = p.Get().(int)
	p.Put(1)
	runtime.GC() //手动调用GC
	b = p.Get().(int)
	fmt.Println(a, b)
}
```
执行结果分别打印出了:
```
0 1
0 0
```	
可见，在手动调用GC之后，Pool中的对象被全部清除了，在Get的时候重新调用定义的New方法创建了新的对象
