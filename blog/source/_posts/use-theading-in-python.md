---
title: python 多进程、多线程简单使用
date: 2018-03-07 14:42:01
tags: [python,thread]
categories: [python]
---

python 中可以使用 multiprocessing (多进程) 和 multiprocessing.dummy(多线程) 来实现并发操作。
两者在使用方式上一样，只不过实现并发的方式不同。

以下简单使用案例以 multiprocessing.dummy 多线程为例：
```python

from multiprocessing.dummy import Pool

def compute(param):
	return param*100

params = [ _ for _ in range(10) ]

pool = Pool()

results = pool.map(compute, params)

pool.close()

pool.join()

print(results)
```

注意：多进程(multiprocessing) 无法在 celery 等后台进程中使用，因为 celery 等后台进程不再允许生成子进程,这时候就只能使用多线程或者协程了。

```
daemonic processes are not allowed to have children
```