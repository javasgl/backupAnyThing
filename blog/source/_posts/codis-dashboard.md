---
title: codis dashboard 无法启动处理流程
date: 2017-08-31 15:03:20
tags: [codis,zookeeper]
categories: [linux]
---

使用 codis 过程中，当 codis dashboard 异常挂掉之后，重启 codis-dashboard的时候，无法启动，日志报错信息为：
```
2017/08/31 14:43:46 dashboard.go:234: [PANIC] create zk node failed
[error]: dashboard already exists: {"addr": "192.168.0.1:28087", "pid": 15716}
```
提示无法创建 zookeeper node,因为已经存在 dashboard node 了。

处理方案：连上 zookeeper, 删除相应的节点后重新启动 codis-dashboard。
```
/usr/local/zookeeper/bin/zkCli.sh -server 192.168.0.1:2181
delete /zk/path/dashboard
```
codis-dashboard 重新起来之后，发现还是无法连接 codis。
<!--more-->
```
2017/08/31 14:44:53 dashboard.go:160: [INFO] dashboard listening on addr: :28087
2017/08/31 14:44:53 dashboard.go:143: [INFO] dashboard node created: /zk/codis/path/dashboard, {"addr": "192.168.0.1:28087", "pid": 10261}
2017/08/31 14:44:53 dashboard.go:144: [WARN] ********** Attention **********
2017/08/31 14:44:53 dashboard.go:145: [WARN] You should use `kill {pid}` rather than `kill -9 {pid}` to stop me,
2017/08/31 14:44:53 dashboard.go:146: [WARN] or the node resisted on zk will not be cleaned when I'm quiting and you must remove it manually
2017/08/31 14:44:53 dashboard.go:147: [WARN] *******************************
2017/08/31 14:45:09 proxy.go:191: [INFO] mark_offline, check proxy status:proxy_1<nil> zk: node does not exist
2017/08/31 14:45:09 proxy.go:193: [INFO] shutdown proxy successful
```
这时候需要访问 http://192.192.168.0.1:28087/admin ，找到最后的 proxy status。将相应的 proxy Mark Online 即可。
启动日志中也提到了，对于 kill -9 pid 停止 codis 的情况，需要手动删除 zookeeper 相应的节点。

zookeeper 常用命令，使用 help 查看:
```
ZooKeeper -server host:port cmd args
	stat path [watch]
	set path data [version]
	ls path [watch]
	delquota [-n|-b] path
	ls2 path [watch]
	setAcl path acl
	setquota -n|-b val path
	history
	redo cmdno
	printwatches on|off
	delete path [version]
	sync path
	listquota path
	rmr path
	get path [watch]
	create [-s] [-e] path data acl
	addauth scheme auth
	quit
	getAcl path
	close
	connect host:port
```


