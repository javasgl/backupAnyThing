---
title: 阅读 php 源码，使用 vld 扩展查看 opcode
date: 2017-08-03 10:12:44
tags: [opcode,php]
categories: php
---

php 虽然是一门动态语言，但是它也有一个编译的过程，这个过程经过词法分析、语法分析,最终生成 Zend 引擎可以执行的 opcode 指令，类似于 java 的字节码。

要查看一段代码对应的 opcode ，可以使用 [vld 扩展](http://pecl.php.net/package/vld/) 来查看。

## 安装

安装 vld 和安装 php 其他的扩展没有任何区别，下载源码、编译、安装等...
```
tar zxf vld-0.14.0.tar.gz
cd vld-0.14.0
phpize
./configure
make
make install

```
记得要修改 php.ini 启用 vld 扩展:
```
[vld]
extensions=vld.so
```
<!--more-->

## 使用

使用 vld 查看 opcode 也非常简单，主要传递参数`-dvld.active=1`来激活 vld 扩展即可：
```
[root@host ~]# php -dvld.active=1 source.php
Finding entry points
Branch analysis from position: 0
Jump found. (Code = 62) Position 1 = -2
filename:       /root/source.php
function name:  (null)
number of ops:  3
compiled vars:  !0 = $a
line     #* E I O op                           fetch          ext  return  operands
-------------------------------------------------------------------------------------
   2     0  E >   EXT_STMT
         1        ASSIGN                                                   !0, 10
   3     2      > RETURN                                                   1

branch: #  0; line:     2-    3; sop:     0; eop:     2; out1:  -2
path #1: 0,
```
## 参数

vld 扩展主要有以下几个参数：

 - vld.active=1 启用 vld 扩展
 - vld.verbosity=3 显示更详细信息
 - vld.execute=0 只显示 opcode 而不执行 php 脚本

还有一些其他参数，请参考 [深入理解 php 内核#VLD 扩展使用指南](http://www.php-internals.com/book/?p=C-php-vld)

## 备注

如果经常需要查看 opcode , 而每次在命令中传递参数避免麻烦的话，可以将以上 vld 的参数配置在 php.ini 文件中，
```
[vld]
extensions=vld.so
vld.active=1
vld.vld.verbosity=3
vld.execute=0

```
从而避免手动使用 `php -d` 参数来传递 vld 配置。
```
php --help
...
 -d foo[=bar]     Define INI entry foo with value 'bar'
...
```