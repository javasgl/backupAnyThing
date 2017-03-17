---
title: Linux zgrep使用笔记
date: 2017-03-17 17:20:05
tags: [Linux,zgrep]
categories: [notes]
---

`grep` 可以用来搜索文件内容,而基于`grep`的 `zgrep`这可以用来在压缩包中搜索内容而不需要事先解压文件.

```
[root@yourdream ~]# cat test.go
package main
import (
   "fmt"
)
func main(){
  fmt.Println("Hello Golang!");
}

[root@yourdream ~]# tar zcf test.tar.gz test.go

[root@yourdream ~]# file test.tar.gz
test.tar.gz: gzip compressed data, from Unix, last modified: Fri Mar 17 17:23:09 2017

[root@yourdream ~]# zgrep "fmt" test.tar.gz
Binary file (standard input) matches

[root@yourdream ~]# zgrep -a "fmt" test.tar.gz
   "fmt"
  fmt.Println("Hello Golang!");

[root@yourdream ~]# tar -tvf test.tar.gz
-rw-r--r-- root/root        81 2017-03-17 17:18 test.go

[root@yourdream ~]# tar -rvf test.tar.gz test/
test/
test/a.php

[root@yourdream ~]# tar -tvf test.tar.gz
-rw-r--r-- root/root        81 2017-03-17 17:18 test.go
drwxr-xr-x root/root         0 2017-03-17 17:50 test/
-rw-r--r-- root/root      1127 2017-03-17 17:50 test/a.php

[root@yourdream ~]# zgrep -aHn "Hello" test.tar.gz
test.tar.gz:6:  fmt.Println("Hello Golang!");
test.tar.gz:16:echo 'Hello php';

```

- 注意需要加上 `-a` 参数,让其二进制文件当做文本处理
- `tar -u -r` 先压缩包中添加文件
- `tar -H` 显示文件名
- `tar -n` 显示行号
