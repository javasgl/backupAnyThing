---
title: 使用 Elasticsearch alias 功能切换 index
date: 2017-12-20 17:20:11
tags: [elasticsearch,es,alias]
categories: [elasticsearch]
---
有时候，需要将已有的索引做些调整重建为另外一个索引，但是为了不影响线上的访问，需要无缝切换到新的索引上。

基本上有两种方案，一是改代码，使线上代码访问新的索引，二是使用 alias 别名功能。

第一种方案有弊端，在代码进行发布到具体机器之前的并不能保证所有的访问都是访问的新的索引。

这里选择通过 alias 来切换索引，因为 alias 中的多条命令是原子性。

<!--more-->

1.创建索引 test 和 test_v2
```
PUT test
PUT test_v2
GET test*
```
结果如下:
```
{
  "test": {
    "aliases": {},
    "mappings": {},
    "settings": {
      "index": {
        "creation_date": "1514183636616",
        "number_of_shards": "5",
        "number_of_replicas": "1",
        "uuid": "oOgEhLzqS_usf7floIhNig",
        "version": {
          "created": "5030099"
        },
        "provided_name": "test"
      }
    }
  },
  "test_v2": {
    "aliases": {},
    "mappings": {},
    "settings": {
      "index": {
        "creation_date": "1514183638407",
        "number_of_shards": "5",
        "number_of_replicas": "1",
        "uuid": "o6x6VtmQSFaOpOB-OpIwBg",
        "version": {
          "created": "5030099"
        },
        "provided_name": "test_v2"
      }
    }
  }
}
```
2.现在需要将对 test 的访问无缝切换到 test_v2,使用 alias 功能即可做到，对线上服务、对用户无感
```
POST _aliases
{
  "actions": [
    {
      "add": {
        "index": "test_v2",
        "alias": "test"
      }
    },
    {
      "remove_index": {
        "index": "test"
      }
    }
  ]
}
```
actions 中的多条命令是原子性的，所以可以做到无缝切换，不必担心切换过程中会存在访问不到 test 时候。

3.命令解释
actions 中第一条命令：add 命令，给索引 test_v2 添加了一个别名 test。
actions 中第二条命令：remove_index， 删除原来的 test 索引。 
执行之后，原来的 test索引会被删除，而对于访问方而言,访问的索引依然是 test,不过访问的是一个别名，底层实际访问的索引已经是 test_v2了

```
GET test*

{
  "test_v2": {
    "aliases": {
      "test": {}
    },
    "mappings": {},
    "settings": {
      "index": {
        "creation_date": "1514183638407",
        "number_of_shards": "5",
        "number_of_replicas": "1",
        "uuid": "o6x6VtmQSFaOpOB-OpIwBg",
        "version": {
          "created": "5030099"
        },
        "provided_name": "test_v2"
      }
    }
  }
}
```
从结果中可见，test索引已经不存在了，但是通过 GET test 还是能访问到,而现在 test_v2 多了一个 alias test。

