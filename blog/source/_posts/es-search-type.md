---
title: Elasticsearch 几种 Search Type 
date: 2017-08-24 16:45:45
tags: [elasticsearch]
categories: [elasticsearch]
---

Search Type 有助于理解 Elasticsearch 在多个分片(shard)的情况下，是如何搜索的，是如何决定返回数据数量、如何排序、如何算分的？

## 执行过程

在多个分片情况下，查询 0-20 条记录，elasticsearch 会将请求转发到每个分片上，从每个分片上获取 20 条记录，然后返回给分发节点进行汇总，最后再从汇总的结果中取 0-20 数据返回给客户端。
则就是 elasticsearch 分布式搜索的基本的执行过程。

## Search Type

Elasticsearch 中的 search type 主要有 6种：Query then fetch、Dfs Query then fetch、Count、Scan、Query and fetch、Dfs query and fetch。

<!--more-->

执行请求的过程中，涉及到几个不同的 search type。

- Query then fetch
	查询主要分为两步:
	第一步，查询节点首先将请求转发给所有参与的节点，每个节点依据自身的数据执行查询，并将必要的信息返回给查询节点,查询节点进行合并、重新排序等操作。
	第二步，查询节点仅从相关的节点获取数据返回。

	如果没有在请求中没有指定 search_type 参数这这是默认的方式。

- Dfs Query then fetch

	和 Query then fetch 一样，只不过在第一步转发请求的时候，它将计算分布式词频来获得更准确的得分。

- Count

	顾名思义，这个并不返回文档数据，只返回文档的数量。

- Scan

	适用于大量数据进行深度分页获取数据,禁用排序提升性能。

- Query and fetch

	这是 elasticsearch 内部的一种优化，不需要手动指定。当请求只作用在一个节点上面的是否，Query then fetch 的两步就合并为一步执行了。当我们有时候为了避免聚合(Aggregation)不准确而将 numbers_of_shards 设置为1个的时候，所有的请求就只在一个节点上处理。

- Dfs Query and fetch

	同 Dfs Query and fetch，也不需要手动执行，请求作用于单个节点时候的一种内部优化机制。
