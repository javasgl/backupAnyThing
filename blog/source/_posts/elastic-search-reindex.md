---
title: 使用 reindex 来修改 elasticsearch 索引mapping
date: 2018-04-02 08:32:00
tags: [elasticsearch,reindex]
categories: [elasticsearch]
---

elasticsearch索引一旦建立，就无法动态修改其字段的映射类型，有时候因为人为原因污染了索引的mapping,这个时候就只能通过重建索引来修改索引的mapping设置了。

在一次项目中，有一个字段结构如下：
```
{
	"logistics":{
		"company":"string",
		"no":"string"
	}
}
```
由于当初创建索引的时候，既没有给这个索引中的这个字段指定合适的类型，也没有通过动态模板来为这个字段指定类型，导致 elasticsearch 默认将这种结构的数据设置成了 Object 类型，而我们真正想要设置的类型却是 Nested 类型。

这种情况下，一般有两种解决方案。

第一种，给这个索引追加一个新的字段，同时给这个字段指定类型。
```
PUT test/_mapping/test
{
  "properties": {
    "logisticsV2":{
      "type": "nested",
      "properties": {
        "company":{
          "type":"keyword"
        },
        "no":{
          "type":"keyword"
        }
      }
    }
  }
}
```
执行之后，index的mapping中就是这样的了：
```
{
　　"test":{
　　　　"mappings":{
　　　　　　"test":{
　　　　　　　　"properties":{
　　　　　　　　　　"logistics":{
　　　　　　　　　　　　"properties":{
　　　　　　　　　　　　　　"company":{
　　　　　　　　　　　　　　　　"type":"text",
　　　　　　　　　　　　　　　　"fields":{
　　　　　　　　　　　　　　　　　　"keyword":{
　　　　　　　　　　　　　　　　　　　　"type":"keyword",
　　　　　　　　　　　　　　　　　　　　"ignore_above":256
　　　　　　　　　　　　　　　　　　}
　　　　　　　　　　　　　　　　}
　　　　　　　　　　　　　　},
　　　　　　　　　　　　　　"no":{
　　　　　　　　　　　　　　　　"type":"text",
　　　　　　　　　　　　　　　　"fields":{
　　　　　　　　　　　　　　　　　　"keyword":{
　　　　　　　　　　　　　　　　　　　　"type":"keyword",
　　　　　　　　　　　　　　　　　　　　"ignore_above":256
　　　　　　　　　　　　　　　　　　}
　　　　　　　　　　　　　　　　}
　　　　　　　　　　　　　　}
　　　　　　　　　　　　}
　　　　　　　　　　},
　　　　　　　　　　"logisticsV2":{
　　　　　　　　　　　　"type":"nested",
　　　　　　　　　　　　"properties":{
　　　　　　　　　　　　　　"company":{
　　　　　　　　　　　　　　　　"type":"keyword"
　　　　　　　　　　　　　　},
　　　　　　　　　　　　　　"no":{
　　　　　　　　　　　　　　　　"type":"keyword"
　　　　　　　　　　　　　　}
　　　　　　　　　　　　}
　　　　　　　　　　}
　　　　　　　　}
　　　　　　}
　　　　}
　　}
}
```
其中 `logisticsV2`的类型是 nested 类型，之后依赖于 nested 类型的相关功能使用 logisticsV2字段来实现即可。
这个中方式有一些弊端，比如数据冗余问题、数据一致性问题等