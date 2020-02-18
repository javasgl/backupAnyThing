---
title: mysql5.7/mariadb Generated Columns
date: 2020-02-18 08:31:45
tags: [mysql,mariadb,sql]
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
注：
> 默认是 VIRTUAL,即 虚拟列，不实际占用存储空间，在读取数据时即时计算而来；
> STORED 则与之相反，它会生成真实的数据列，和正常的列无异，占用存储空间;
> 两者在限制上各有细微区别，具体可查看文末 mysql/mariadb 链接阅读详情

以下样例,展示了自动生成指定时间字段的月份:
<!--more-->
`c_month` 的值通过对`c_time`进行内置函数`month`调用自动得到,插入语句中无需指定`c_month`的值
```
mysql> CREATE TABLE generated_columns_test (c_time datetime,c_month int generated always as(month(c_time)));
Query OK, 0 rows affected (0.19 sec)

mysql> desc generated_columns_test;
+---------+----------+------+-----+---------+-------------------+
| Field   | Type     | Null | Key | Default | Extra             |
+---------+----------+------+-----+---------+-------------------+
| c_time  | datetime | YES  |     | NULL    |                   |
| c_month | int(11)  | YES  |     | NULL    | VIRTUAL GENERATED |
+---------+----------+------+-----+---------+-------------------+
2 rows in set (0.01 sec)

mysql> insert into generated_columns_test(c_time)values (now());
Query OK, 1 row affected (0.00 sec)

mysql> select * from generated_columns_test;
+---------------------+---------+
| c_time              | c_month |
+---------------------+---------+
| 2020-02-18 09:33:42 |       2 |
+---------------------+---------+
1 row in set (0.00 sec)
```

是不是很熟悉？以前这样的需求，一般需要在程序中处理好月份后和 `c_time` 一并插入数据库，是 `generated columns` 则可以做自动生成,将程序逻辑后移到数据库层面了。

可以设想一下，`generated columns` 可用于哪些场景？

- 根据订单创建时间，自动填充默认过期时间？
- 根据发货时间，自动填充有效期？
- ......


当然，`generated columns`并非银弹，不是所有的业务逻辑都适合运用它，不应该把本该程序处理的部分业务交给数据库去处理，这将给系统维护带来隐患，这和不推荐使用存储过程和自定义函数一样的道理

总体来说，`generated columns` 虽然有诸多限制，但总体上使用起来还是很方便的，具体的文档可以查看：

- mysql [generated columns](https://dev.mysql.com/doc/refman/5.7/en/create-table-generated-columns.html)
- mariadb [generated columns](https://mariadb.com/kb/en/generated-columns/)