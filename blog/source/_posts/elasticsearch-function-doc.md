---
title: Elasticsearch function 中 doc , _fields , _source 使用
date: 2017-08-21 12:07:40
tags: [elasticsearch,function]
categories: [elasticsearch]
---

面对复杂的查询或者算分逻辑，Elasticsearch 可以使用 Scrpits 脚本功能来实现，实际项目之中也用的非常频繁。
在脚本中需要访问文档中的字段，此时有三种方式可以访问，分别是 doc , _fields , _source。这三个字段使用的时候还是有区别的。
```
"filtered" : {
    "query" : {
        ...
    },
    "filter" : {
        "script" : {
            "script" : "doc['num1'].value > param1"
            "params" : {
                "param1" : 5
            }
        }
    }
}
```
下面通过几个示例脚本来分别说明三者之间的区别。
<!--more-->
## 范例文档

示例 mapping 如下：
```
{
    "test": {
        "mappings": {
            "test": {
                "_all": {
                    "enabled": false
                },
                "properties": {
                    "categories": {
                        "type": "long"
                    },
                    "name": {
                        "type": "string"
                    },
                    "name2": {
                        "index": "not_analyzed",
                        "type": "string"
                    },
                    "price": {
                        "type": "double",
                        "store": true
                    },
                    "skus": {
                        "properties": {
                            "id": {
                                "type": "long"
                            },
                            "name": {
                                "type": "string"
                            },
                            "price": {
                                "type": "double"
                            }
                        }
                    },
                    "status": {
                        "type": "short"
                    },
                    "updateTime": {
                        "type": "long"
                    }
                }
            }
        }
    }
}

```
文档数据如下：
```
{
    "_id": "1",
    "_index": "test",
    "_source": {
        "categories": [
            1,
            3,
            4
        ],
        "name": "宽松印花中袖香水瓶T恤",
        "name2": "宽松印花中袖香水瓶T恤",
        "price": 123.68,
        "skus": [
            {
                "id": 1,
                "name": "红色",
                "price": 12.88
            },
            {
                "id": 2,
                "name": "蓝色",
                "price": 23.66
            }
        ],
        "status": 1,
        "updateTime": 1503560261
    },
    "_type": "test",
    "_version": 2,
    "found": true
}
```

##  doc

doc 可以很方便的使用文档中的字段，通过 doc['field_name']来访问,访问速度比较快，因为相关的值已经加载到内存中了。
但是它有一些使用限制: 访问的字段不能是 object 类型;只对 not-analyzed 字段有效

例如：对于上述的文档，doc["skus"] 是无效的:
```
"script": "for(sku in doc['skus']){if(sku.price>5){return true;}};return false;" //No field found for [skus] in mapping!
```
而 doc["categories"] 和 doc["price"] 这是可以的:
```
"script": "for(category in doc['categories']){if(category>2){return true;}};return false;"
"script": "doc['price'])>100"
```
doc["name"] 无效是因为 name 为 analyzed 的字段:
```
"script": "doc['name'].value.length()>=10" //not found
```
虽然访问 doc['name']无效，但是并不报错，其实对于这种分词的字段，访问 doc['name'] 其实访问的是分词过后的 token,所以不会报 no field found for [name] in mapping 错误。
doc['name2']有效，因为 name2 字段 是 not_analyzed 的(可以理解为不分词):
```
"script": "doc['name2'].value.length()>=10" //found
```
## _fields

_fields 使用方式同 doc 一样的，不过需要注意的是 _fields 必须是 mapping 中 store 的字段，例如 price 字段：
```
"script": "_fields['price'].value<=param1",
```
同时性能会低于加到内存中的doc。
默认情况下，创建 mapping 时候如果没有指定 store为 true 则 相应的字段是没有 stored 的。

## _source

如果创建 mapping 的时候没有禁用 source , 则在脚本中可以使用 _source 来访问文档内容。_source的内容实质上就是一段 json。所以可以通说 _source.obj2.obj1.field3 来访问。
```
"script": "for(sku in _source.skus){if(sku.price>22){return true;}};return false;",
```
skus 字段是 object 类型的，可以用过 _source.skus 来访问。其实通过 _source['skus']也是可以访问，这两者是否有区别暂不清楚。
由于 _source是每个文档加载、解析再使用的，所以比已经加载到内存中的 doc 慢很多,不过和 _fields 来比则需要区分使用场景。

- 如果访问单个字段，_fields 比 _source 快
- 如果访问多个字段，_source 比 _fields 快
