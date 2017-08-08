---
title: shell脚本修改环境变量
date: 2017-04-28 12:04:50
tags: [Linux,Shell]
categories: Linux
---
有时候编写go程序的时候需要将当前目录加到系统`GOPATH`中，不然就得将代码放置在已有的`GOPATH`之下。所以一般习惯在项目根目录执行创建一个`shell`脚本来动态的修改`GOPTAH`。
脚本很简单，代码如下:
```
#/bin/sh
CURRENT=`pwd`
export GOPATH="${GOPATH}:${CURRENT}"
```

因为是`shell`脚本，所以就习惯性的执行了如下命令:
```
sh env
echo $GOPATH
```
期望`GOPATH`中已经追加上了当前目录，然而事与愿违，并没有什么效果，`GOPATH`仍然是之前的内容。

原因:
使用 `sh` 命令来执行shell脚本的时候,脚本真正是在`sh`创建的子`shell`中执行,所以当`sh`进程完成的时候并没有修改系统变量,所以通过执行 `sh env` 来修改系统变量是无效的。子`shell`和父`shell`彼此无法使用对方的变量，子`shell`对环境变量的修改也不会影响父`shell`。
需要使用 `source env`来执行，`source`命令执行脚本的时候，是在`source`当前`shell`中执行的,并不会创建子`shell`。

参考: [用source 执行脚本和用sh 执行脚本有什么区别](http://bbs.chinaunix.net/thread-2211666-1-1.html)
