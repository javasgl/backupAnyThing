---
title: python asyncio 简单使用
date: 2018-03-07 15:22:37
tags: [python,coroutine,asyncio]
categories: [python]
---

在python 3.4及以上的版本中，可以通过 asyncio 来实现协程(coroutine)。在 python 3.5以后可以使用 async/await 来简化代码，如果python版本是3.4的话，需要通过 `@asyncio.coroutine`注解 和 `yield from` 来实现。

一下代码是3.5+以后的写法，和3.4版本的写法主要区别在于:
1. 用 async 代替了 @asyncio.coroutine 
2. 用 await 代替了 yield from

```python

import asyncio

async def task(n):
	print("do task:",n)
	await asyncio.sleep(1)

tasks = [ task(_) for _ in range(10)]

loop = asyncio.get_event_loop()
res, _ = loop.run_until_complete(asyncio.wait(tasks))
loop.close()
for _ in res:
	print(_.result())

```

参考：
	廖雪峰的Python3.x教程-[异步io](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001432090954004980bd351f2cd4cc18c9e6c06d855c498000)
