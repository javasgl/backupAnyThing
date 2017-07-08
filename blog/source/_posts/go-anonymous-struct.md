---
title: golang 匿名 struct 的使用方式
date: 2017-07-08 23:20:01
tags: ["go","anonymous"]
categories: ["go"]
---

编程中有时候需要一个临时的 struct 来封装数据，而这个 struct 的结构在其它地方又不不会被二次复用,可以使用匿名 struct 来实现。


主要有两种方式,如下:
第一种方式，通过 var 初始化

```
var user struct{Name string;age int}
user.Name = "name"
user.age = 18

```

第二种方式，直接初始化

```
json.Marshal(struct{Name string;age int}{"name",18})

```
或者：
```
json.Marshal(struct{
	Name string
	age int
}{"name",18})
```
