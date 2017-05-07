---
title: dbm--python的一个简易K-V数据库
date: 2017-05-07 21:46:13
tags: [python,dbm,database]
categories: python
---

在一些小型简单的`python`程序中,不需要(不想)引入庞大的关系型数据库或者其他大型的非关系型数据库(NoSql)时，`dbm`模块是一个非常不错的选择。
不过在使用的时候需要注意的是，它要求`key`,`value`均为字符串类型。
可以对其进行一个简单的封装，便于使用。
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import dbm
from models.Config.Config import Config

class DBM(object):
    def __init__(self):
        self._db = dbm.open(Config.parse_config('dbm', 'dbfile'), 'c')

    def set(self, key, value):
        self._db[key] = str(value)
        return self

    def del(self, key):
        if key in self._db.keys():
            del self._db[key]

    def get(self, key):
        if key in self._db.keys():
            return self._db[key]
        else:
            return None

    def __del__(self):
        self._db.close()

```
> Config 是自己封装的一个简易的配置文件解析的类，负责从`ini`文件中读取指定的配置

`dbm` 的 `api` 非常简单，`set`、`del`、`keys`，其数据结构和字典非常相似，可以在小型项目中来当做简易的K/V数据库来使用。