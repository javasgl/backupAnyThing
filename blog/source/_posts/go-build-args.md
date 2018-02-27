---
title: go build 常见编译优化
date: 2018-02-27 16:09:14
tags: [go]
categories: [go]
---

一般情况下，go build 可以直接编译程序，无需额外的参数设定。
但在编译生产环境下使用的可执行程序时候，go build 的一些参数还是很有用的。

## 减小编译后可执行程序的大小

```
go build -ldflags '-w -s'

```
说明：
	-w 禁止生成debug信息,注意使用该选项后，无法使用 gdb 进行调试
	-s 禁用符号表
可以使用 `go tool link --help` 查看 ldflags 各参数含义

## 禁止gc优化和内联
```
go build -gcflags '-N -l'
```
说明:
	-N 禁止编译优化
	-l 禁止内联,禁止内联也可以一定程度上减小可执行程序大小
可以使用 `go tool compile --help` 查看 gcflags 各参数含义
