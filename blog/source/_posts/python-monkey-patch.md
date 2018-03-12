---
title: python monkey patch解决gevent block forever问题
date: 2018-03-12 11:31:53
tags: [monkey,patch,python]
categories: [python]
---

在 python 中使用 multiprocess 进行多线程或者多进程处理的时候，遇到了 gevent 问题：
```python
gevent.hub.LoopExit:('This operation would block forever....')
```

这个问题可以通过打上 monkey patch(猴子补丁)来规避。

在导入 multiprocess 库的文件头部加入以下代码：
```
from gevent import monkey

monkey.patch_all()
```

注意，这两行代码需要放置在文件导入库的最前面.

我遇到的场景下， 导致 LoopExit 的原因是 multiprocess 的job中远程网络请求超时导致的，如果相应的job任务中没有远程请求的话，即使不用 pathch，代码也是能够正常运行的。由于避免加上超时处理的复杂逻辑，就简单的使用monkey patch来解决这个问题。