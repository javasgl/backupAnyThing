---
title: 一个时区引发的`血案` 
date: 2018-06-06 14:10:10
tags: [go,time,timezone,orm,sql]
categories: [go]
---
项目中的一个单元测试，其中一个判断是比较两个时间是否相同。代码逻辑简化抽象如下：
```go
time1:=time.Now()
xorm.engine.NewSession().Update(time1)
var time2 *time.Time
xorm.engine.NewSession().Get(&time2)
if time2.UTC().Format("2018-06-06") != time2.UTC().Format("2018-06-06"){
	panic error
}
```
介绍一下各组件时区设置：
- mysql 默认配置，`SYSTEM`
- xorm dsn 配置为 `parseTime=true&loc=Asia%2FShanghai`

以上代码，在本地机器上跑单元测试时无异常，能正常通过。但是，上了CI环境后，单元测试却失败了。
通过观察CI的日志和自己打印的日志，发现入库前后，这两个时间相差了8个小时，很明显，这个时间受到时区的影响了。

<!--more-->

后来调试发现，本地 mysql time_zone SYSTEM的实际值是 CST (中国标准时间),所以 dsn中指定loc为 `Asia/Shanghai` 并不会有问题。 
而CI环境中，mysql的 time_zone SYSTEM的实际值是 UTC.而此时代码中使用dsn却是`Asia/Shanghai`,这样就会导致 `xorm.Get()`的时候，将时间按照`Asia/Shanghai`来解析，所以，解析出来的结果和UTC时间相差了8个小时。

解决方案：
将代码中使用的dsn改为 parseTime=true&loc=Local 。

最终总结，其实这个问题是可以避免的，起初在设计数据库的时候，时间字段如果没有特殊需求，不应该使用数据库的`date`,`timestamp`等类型，而是统一都使用 `INT UNSIGNED NOT NULL`。这样数字类型的时间戳，即可以避免时区问题，还可以用来直接比较大小和进行算数运算。

附上mysql查看时区配置的几种方式:
```sql
select @@global.time_zone,@@session.time_zone;
```
```sql
show variables like %time_zone%;'
```
