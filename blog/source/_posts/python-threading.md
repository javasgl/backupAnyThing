---
title: python threading 多线程简单使用
date: 2017-09-05 09:13:09
tags: [python,thread]
categories: [python]
---
之前一个项目，使用 Flask 框架写了一个网络图片抓取的程序，当时时间紧急，多张图片下载的时候，就简简单单的使用 `for in` 循环去获取网络图片内容。后来有空了，看了下 python 的多线程的使用，挺简单的，就用 threading 重新改造了下这个程序，让其支持多线程。

<!--more-->

python theading 多线程基本使用方式非常简单:
```
import threading


def hello(*args):
	print "hello"

threads=[]

i=0
while i<10:
	i+=1
	threads.append(threading.Thread(target=hello))



for thread in threads:
	thread.setDaemon(True)
	thread.start()


for thread in threads:
	thread.join()	

```

当时使用 threading 的时候，遇到了一个问题: 如何获取子线程执行结果？
网上搜索一番，基本上有两种方案:
- 继承 threading.Thread, 重载 run 方法。
- 使用外部资源，比如共享存储，队列等方式

项目比较简单，所以直接选择了第一种方式来实现，避免依赖于外部资源。自己封装了一个 CustomThreader 类,代码寥寥几行，非常简单:
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import threading

class CustomThread(threading.Thread):
    def __init__(self, func, args=()):
        super(CustomThread, self).__init__()
        self.func = func
        self.args = args
        self.result = None

    def run(self):
        self.result = self.func(self.args)

    def getResult(self):
        return self.result

```
继承了 threading.Thread, 重载了 run 方法，在 run 方法里面讲执行结果复制给了成员变量 result, 然后对外提供了一个获取返回结果的方法 getResult。
使用方式简单，只需要做一下改动即可：
```
threads.append(CustomThread(func=hello,args=param))

result=[]
for thread in threads:
	result.append(thread.getResult())
```
