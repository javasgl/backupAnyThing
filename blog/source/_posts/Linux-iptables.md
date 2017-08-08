---
title: iptables使用笔记
date: 2017-03-17 15:07:10
tags: [Linux,iptables]
categories: Linux
---

#### Linux 下防火墙 `iptables` 命令使用笔记

- 查看目前iptables设置 `iptables -vnL --line-numbers`
 - `-v` verbose 详细信息
 - `-n` 显示数字端口等
 - `-L` list ,默认会显示`filter`表的规则
 - `--line-numbers` 显示序号,删除、插入时有用

- 删除规则`iptables -t table -D INPUT ruleNumber`
  - `-t` table name,默认filter表,filter|Nat|Mangle|Raw
  - `-D` delete
  - `INPUT` chain name,INPUT|OUTPUT|FORWARD|PREROUTING|POSTROUTING
  - `ruleNumber` rule number,可以使用`--line-numbers`参数查看

- 查看状态`/etc/init.d/iptables status`
- 保存修改`/etc/init.d/iptables save`,规则保存在`/etc/sysconfg/iptables`文件中
- 开启|重启|关闭`/etc/init.d/iptables start|restart|stop`

<!--more-->

#### examples

`iptables -I INPUT -s 1.1.1.1 -p tcp -dport 6666 -m comment --comment "some comment" -j ACCEPT`
- 向`INPUT`链中`I`(插入)一条规则,没有指定ruleNumber则插入到最前面
- `-s` source,来源ip或者hostname
- `-p` protocol 协议 tcp,udp,icmp(ping包),all 等 `/etc/protocols`中protocol的均可以
- `-dport` 目标端口 destination port,注意`--dport`和`-dport`
- `-m` match extension,启用扩展, `comment`是注释扩展
- `-j` jump
- `ACCEPT` ACCEPT|DROP|REJECT|LOG

详细可参考 [iptables详解](http://www.cnblogs.com/metoy/p/4320813.html),文章很详细!
