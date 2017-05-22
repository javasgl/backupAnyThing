---
title: mysql unsigned/int字段越界
date: 2017-05-22 10:41:39
tags: ["database","mysql"]
categories: ["mysql"]
---
线上的`MySQL`错误日志中遇到了以下问题
```
Data truncation: BIGINT UNSIGNED value is out of range in `(database.tablename.fieldname)`
```
`MySQL`版本为:
```
mysql> select version();
+------------+
| version()  |
+------------+
| 5.6.21-log |
+------------+
1 row in set (0.00 sec)
```
引起这个错误的原因是`sql`语句中对`unsigned`字段进行了递减操作的结果为负数导致的,
```
update database.tablename set fieldname=fieldname-num
```
而`MySQL`的模式是默认的`mode`:
```
mysql> show variables like 'sql_mode';
+---------------+------------------------+
| Variable_name | Value                  |
+---------------+------------------------+
| sql_mode      | NO_ENGINE_SUBSTITUTION |
+---------------+------------------------+
1 row in set (0.00 sec)
```
解决方案有3种:

1.程序中做数据合法性检查，先查询出来，递减之后判断结果，结果正常再去插入，不过这样需要2次`sql`操作，需要保证操作的原子性，不然容易引起数据一致性问题.

2.在同一个`session`中临时修改`MySQL`的`sql_mode`:
```
SET sql_mode='NO_UNSIGNED_SUBTRACTION';
UPDATE database.tablename SET fieldname=fieldname-num

```
`NO_UNSIGNED_SUBTRACTION` 模式下，对于`unsigned`字段，如果插入负值，则会讲该字段的值自动设置为 0 .

3.如果`unsigned`字段能接受负值,则可以使用`CAST`函数处理:
```
SET sql_mode='NO_UNSIGNED_SUBTRACTION';
UPDATE database.tablename SET fieldname=CAST(fieldname-num AS UNSIGNED);
```
需要注意的是`CAST`的表现会因不同的`sql_mode`而不同，需要合适的`sql_mode`和`CAST`配合使用.

参考:
- [mysql out-of-range-and-overflow](https://dev.mysql.com/doc/refman/5.6/en/out-of-range-and-overflow.html)
- [mysql sql mode](https://dev.mysql.com/doc/refman/5.6/en/sql-mode.html)
- [mysql no_unsigned_subtraction mode](https://dev.mysql.com/doc/refman/5.6/en/sql-mode.html#sqlmode_no_unsigned_subtraction)
