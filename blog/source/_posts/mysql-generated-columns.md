---
title: mysql5.7+ Generated Columns
date: 2020-02-18 08:31:45
tags: [mysql,sql]
categories: mysql
---

mysql 5.7 以后带来了一个新的功能，即 "Generated Columns",字面意思可翻译为 "生成列"，顾名思义，这列的数据不能被直接插入或者更新，而是依据一定的规则自动生成。


Generated Columns 定义方式如下:
```
col_name data_type [GENERATED ALWAYS] AS (expr)
  [VIRTUAL | STORED] [NOT NULL | NULL]
  [UNIQUE [KEY]] [[PRIMARY] KEY]
  [COMMENT 'string']
```

样例：
```
```