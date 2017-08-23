---
title: 使用 redis-check-aof 命令修复 aof 文件
date: 2017-08-23 18:27:30
tags: [redis,aof]
categories: [redis]
---

偶然的一天，发现redis一个实例无法启动，启动的时候，从 aof 文件往内存中加载数据的时候，出现了错误：

```
Bad file format reading the append only file: make a backup of your AOF file, then use ./redis-check-aof --fix filename
```

提示，需要使用 redis-check-aof 来修复 aof 文件。

按照其步骤，先备份再修复：
```
[root@hostname 6379]# redis-check-aof --fix appendonly.aof
0x        6a60a5cb: Expected prefix '
AOF analyzed: size=1784722401, ok_up_to=1784718795, diff=3606
This will shrink the AOF from 1784722401 bytes, with 3606 bytes, to 1784718795 bytes
Continue? [y/N]: y
Successfully truncated AOF
```

修复完成后，重新启动 redis 就能正常启动了。

同样，redis 如果采用的 rdb 模式持久化数据的话，如果需要修复 rdb 文件，可以使用 redis-check-dump file.rdb 来修复。
