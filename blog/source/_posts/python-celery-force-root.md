---
title: python celery 强制使用root用户执行
date: 2017-12-19 15:20:22
tags: [python,celery,supervisor]
categories: [python]
---

当 celery 使用  root 用户执行时，会报出如下警告:
```
RuntimeWarning: You're running the worker with superuser privileges: this is absolutely not recommended!

```

低版本的 celery 情况不清楚，但是 celery 4.1.0 版本中，这个警告是无法去除的。因为源码中是这样写的:

<!--more-->

```python
def check_privileges(accept_content):
    uid = os.getuid() if hasattr(os, 'getuid') else 65535
    gid = os.getgid() if hasattr(os, 'getgid') else 65535
    euid = os.geteuid() if hasattr(os, 'geteuid') else 65535
    egid = os.getegid() if hasattr(os, 'getegid') else 65535

    if hasattr(os, 'fchown'):
        if not all(hasattr(os, attr)
                   for attr in ['getuid', 'getgid', 'geteuid', 'getegid']):
            raise SecurityError('suspicious platform, contact support')

    if not uid or not gid or not euid or not egid:
        if ('pickle' in accept_content or
                'application/x-python-serialize' in accept_content):
            if not C_FORCE_ROOT:
                try:
                    print(ROOT_DISALLOWED.format(
                        uid=uid, euid=euid, gid=gid, egid=egid,
                    ), file=sys.stderr)
                finally:
                    os._exit(1)
        warnings.warn(RuntimeWarning(ROOT_DISCOURAGED.format(
            uid=uid, euid=euid, gid=gid, egid=egid,
        )))

```
很明显，这个 RuntimeWarning 是无法通过 C_FORCE_ROOT 设置来屏蔽的。当然了，这个只是一个警告，celery的运行并不受影响。

如果遇到某些版本的 celery 因为 root 用户而无法启动、无法执行的情况，可以通过改变 C_FORCE_ROOT=True 来规避这个问题。

主要有四种方式:

1.手动设置环境变量 
```
export C_FORCE_ROOT="true"
```
2.使用 supervisor 管理 celery 时，可以用 environment 来设置环境变量. 
```
environment=PYTHONPATH="xxx",C_FORCE_ROOT="true"
```
3.代码中硬编码指定.
```python
from celery import platforms

platforms.C_FORCE_ROOT=True
```
4.写入 bashrc 或者其他系统启动脚本之中
```
file:.bashrc
export C_FORCE_ROOT="true"
```

