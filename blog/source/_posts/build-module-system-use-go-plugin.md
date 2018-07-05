---
title: 用 Go Plugin 构建模块化系统 
date: 2018-07-05 16:48:13
tags: [go,plugin]
categories: [go]
---
自从 golang 1.8以后，提供了一个 Plugin 的机制使得 golang能够加载 so 动态链接库文件。
这样可以对外发布动态链接库而不需要把源码共享给对方去进行编译,还可以做到按需加载相应的插件来实现动态开启插件相应的功能。

官方文档在此：https://golang.org/pkg/plugin/ 

<!--more-->

编写一个 Plugin 基本有以下几步:

1.Plguin 需要有自己的 main package
2.编译的时候，使用 `go build -buildmode=plugin file.go` 来编译
3.使用 `plugin.Open(path string)` 来打开.so文件，同一插件只能打开一次，重复打开会报错
4.使用 `plugin.LookUp(name string)` 来获取插件中对外暴露的方法或者类型
5.使用类型断言，断言后执行相应的方法

main.go
```
package main

import (
	"fmt"
	"plugin"
)

func main() {

	p, err := plugin.Open("plugin.so")
	if err != nil {
		panic(err)
	}
	m, err := p.Lookup("GetProductBasePrice")
	if err != nil {
		panic(err)
	}
	res := m.(func(int) int)(30)
	fmt.Println(res)
}
```
plugin.go
```
package main

import "fmt"

func main() {

}

func GetProductBasePrice(basePrice int) int {

	return basePrice + 200
}

```
注意几点问题：
1. 插件中定义的 struct 无法暴露出来，可以让主程序和插件程序import公共的 package 来解决
2. 私有方法、变量不会被暴露出来
