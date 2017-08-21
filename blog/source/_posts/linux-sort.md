---
title: Linux sort 命令使用
date: 2017-08-21 11:18:53
tags: [Linux,sort]
categories: [Linux]
---

Linux 可使用 sort 命令来对文件内容或者其他命令的输出内容进行排序。常见的场景是按大小排列日志文件、按文件名排序等。

sort 命令常用的参数有：
- r sort默认排序为升序，如果需要降序，则执行 -r即可。reverse
- n sort排序默认是当做字符来排序的，所以会遇到 10<2 的情况,使用 n 让其当做 number 来排序
- u 去重,排重
- t 指定分割符号，有时候需要将内容分割，按照其中部分内容排序
- k 指定排序列，一般配合 t 使用，注意，列从 1 开始
- o 将输出结果重定向写入文件，写入原文件时不能使用 > 。


example:
```
[root@linux]$ls search2*|sort -rn -k 4 -t '.'
search2.stdout.log.20
search2.stdout.log.19
search2.stdout.log.18
search2.stdout.log.17
search2.stdout.log.16
search2.stdout.log.15
search2.stdout.log.14
search2.stdout.log.13
search2.stdout.log.12
search2.stdout.log.11
search2.stdout.log.10
search2.stdout.log.9
search2.stdout.log.8
search2.stdout.log.7
search2.stdout.log.6
search2.stdout.log.5
search2.stdout.log.4
search2.stdout.log.3
search2.stdout.log.2
search2.stdout.log.1
search2.stdout.log
```
