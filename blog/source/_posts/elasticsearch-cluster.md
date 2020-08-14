---
title: ES7.8.1 集群搭建踩坑记录
date: 2020-08-14 22:44:15
tags: [elasticsearch,cluster]
categories: [elasticsearch]
---
时隔两年多时间，再次接触 elasticsearch,准备在测试环境搭建一套 ES 集群用来测试

之前使用ES还是在其1.4.x~2.0.x左右的时候，现在 ES 版本已经发展到了7.x以上了(中间经历过于kibana同步版本号,所以版本变动比较大)

## 单机安装
大致安装步骤和之前的版本差别不大,依然保持了其开箱即用的特性,只需要很少的配置即可

单机很简单，修改集群名称，节点名称,数据及日志目录,以及监听绑定的ip,然后直接运行可执行文件即可

```
注：下载的打包文件中，附带一个openjdk可以直接使用，如果使用 supervisor 来管理 es, 可以使用
supervisor 的 environment 功能，来设置 JAVA_HOME 环境变量

配置如下，ES_HOME 即 elasticsearch 解压后文件夹所在目录

[es]
command=$ES_HOME/bin/elasticsearch
directory=$ES_HOME
environment=JAVA_HOME=$ES_HOME/jdk
```

单机没什么问题，很顺畅的就启动起来了，通过 curl http://ip:es_port 可以得到很熟悉的返回：
```
{
  "name" : "node1",
  "cluster_name" : "cluster-name",
  "cluster_uuid" : "2c2jICSxTHK_jUcJjaBucw",
  "version" : {
    "number" : "7.8.1",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "b5ca9c58fb664ca8bf9e4057fc229b3396bf3a89",
    "build_date" : "2020-07-21T16:40:44.668009Z",
    "build_snapshot" : false,
    "lucene_version" : "8.5.1",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}

```
## 组建集群
既然单机的起来的，那如法炮制，将配置文件复制一份，修改了其 node name, scp到另外一台机器上，准备好 supervisor 配置，准备开始组建两个节点组成的集群

满以为不会有什么问题，启动之后，发现，第二个节点，怎么也加入不到第一个节点之中
<!--more-->

问题出现了，看了看文档，发现自 es7.x版本以后，更新换代了一个集群协调的功能工具 zen, 简化了很多配置项，组建集群时，
基本上就需要配置两个：
> discovery.seed_hosts, cluster.initial_master_nodes

seed_hosts: 启动当前节点时，用于发现其他节点的初始列表
initial_master_nodes: 初始的候选master节点列表


最开始的时候，这两个均配置了两台机器的ip,即[node1,node2],分别启动节点后，发现后启动的节点，怎么也加入不到之前启动的节点中，组成集群

两个节点，脑裂了~~~~

网上搜了很多解决方案，绝大多数是说不该监听0或者0.0.0.0，遂调整配置，再次启动，发现依然无法解决两个节点脑裂问题

后来仔细研读了下，文档中对 initial_master_nodes 的说明，发现了问题：
> initial_master_nodes 中配置了当前节点，即意味着，当前节点也是可以候选的可用的master节点，但其实，当前节点都还没启动，它是没有资格去竞选成为master的，所以导致第二个节点启动后，自己独立成群，无法与已有节点组成集群

问题找到了，修改第二个节点的配置文件，initial_master_nodes 仅指定第一个节点为可用的候选 master节点

随后启动节点二，启动正常，节点二和节点1成功组成一个集群

> 后来，将 network.host 改为监听 0.0.0.0,两者依然可以组成集群，可见，网传的不能监听0.0.0.0的说法是不成立的