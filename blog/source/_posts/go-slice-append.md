---
title: golang中slice越界问题
date: 2017-06-21 00:02:26
tags: ["go","slice"]
categories: go
---

`go`中`slice`由于其类似于一个长度可动态变化的数组而非常方便使用。
`slice`有两个基本属性：`cap`,`len`，分别代表容量和已有数据长度。
一般声明`slice`的两种方式如下：
```go
//定义了一个初始长度为0,容量为2的int 类型的 `slice`
slice:=make([]int,0,2)

var slice=[]int{0,1}

```

往`slice`中添加元素使用`append`方法：
```go
slice=append(slice,1)
```
<!--more-->

当然有时也可以使用索引值来直接赋值：
```
slice[0]=1
```
不过这种方式需要注意的是，索引的值不能大于`slice`的容量(cap)，否则会报出`panic: runtime error: index out of range`错误。所以一般情况下应该优先使用`append`的方法给`slice`添加元素。

同样，对`slice`或者`array`进行切片(slice)操作的时候，索引也不能超过当前`slice`的容量或者`array`的长度
```
	var slice = []int{0, 1, 2, 3, 4, 5, 6}
	fmt.Println(slice[:5]) //[0,1,2,3,4]
	fmt.Println(slice[1:10]) //slice bounds out of range

```

`slice`的容量扩容规则： 当容量不够时，容量扩充为之前容量的2倍


在调用`append`方法给`slice`添加元素的时候，可以一次添加多个或者全部：
```
var slice = []int{0,1}
var slice2 = []int{2,3}

fmt.Println(append(slice,slice2...)) //[0,1,2,3]

fmt.Println(append(slice,4,5)) //[0,1,4,5]

```