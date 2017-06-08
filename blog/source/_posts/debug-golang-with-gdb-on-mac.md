---
title: Mac上使用gdb单步调试golang程序
date: 2017-06-07 22:29:40
tags: [go,gdb,debug]
categories: [go]
---

## 单步调试

最近在写`golang`程序的时候，想要想单步调试一下程序，减少手动`print log`调试代码的痛苦。

找了一些golang单步调试的方案，主要有几下集中方案:

- 使用 `SublimeGDB` 插件在`Sublime` 中调试
- 使用 `Goland IDE` 调试,不过可惜`Goland`还未发布正式版本
- 使用 `gdb` 工具调试

每个都尝试了一遍:
- `SublimeGDB` 界面不错，但是调试速度太慢，设置一个断点需要很长时间，故 pass 此方案。
- `Goland IDE` 只是预览版，有使用时间限制,还是等等吧，等 `Jetbrains` 出正式版后再去试试.(`Jetbrains`出品必属精品)
- `gdb`调试，使用下来发现，目前而言，`gdb`的方式还是用的比较顺手的。


不过在`mac`电脑上使用`gdb`还是遇到了一些问题,不过还好都顺利解决了。
<!--more-->

遇到的主要问题有权限问题和 mac系统版本问题

## 权限问题
mac 系统为了安全起见，不允许普通用户直接运行gdb,运行时会出现以下问题：
```
Unable to find Mach task port for process-id 4263: (os/kern) failure (0x5).
 (please check gdb is codesigned - see taskgated(8))
```
这个问题，网络上已有解决方案，比如对gdb进行签名啥的，嫌麻烦，没有尝试。详细步骤见[这里](https://sourceware.org/gdb/wiki/BuildingOnDarwin)
这里直接使用 `sudo gdb ..`来运行。可以自行设置命令alias。

```
alias gdb='sudo gdb'
```
不过这样就得每次输入系统密码了。不过可以使用`expect` 来让命令行自动输入密码。

## 版本问题
开始调试的时候遇到了一下问题：
```
During startup program terminated with signal ?, Unknown signal.
```
查询了一下，Mac Sierra 这个版本的系统不支持 gdb 调试。解决方案五花八门。
主要进行了一下操作：
```
brew upgrade gdb 
touch ~/.gdbinit
cat 'set startup-with-shell off' > ~/.gdbinit
```
这些操作主要是:
- 将 gdb 从 7.1.2升级到最新的 8.0
- 给 gdb 写入配置文件

幸运的是，经过这两步的处理，gdb 已经能够在本人机器上正常运行了。

下面记录下一些 常用的gdb操作:

|command|alias|description|
|:-|:-|:-:|
|l main.main|list main.main|查看 golang main函数入口源码|
|b 12|breakpoints 12|在12行设置一个断点|
|d|delete|删除所有断点|
|i b|info breakpoints|查看已经设置的断点|
|r|run|开始运行程序|
|n|next|执行下一行，相当于step over|
|s|-|进入函数内部，相当于step into|
|i locals|info locals|查看局部变量|
|i args|info args|查看参数|
|p paramName|print paramName|打印变量|
|c|-|继续执行，直到下一个断点|
|whatis paramName|-|查看变量类型|
|up|-|返回上一次函数|
|down|-|进入下一层函数|
|h|help|help|
