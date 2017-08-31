---
title: json-iterator--更快的json解析库
date: 2017-08-31 15:24:51
tags: [go,json,json-iterator,encoding,fastjson]
categories: [go]
---

golang 中自带了非常方便解析json 的库 `encoding/json`,一般情况下也是使用这个库来作为 json 的 encode / decode。但是在有些场景下，比如解析大量json格式的日志数据的时候，json的解析性能问题就是一个需要特别关注的问题了。

滴滴出行平台技术部的开源json解析库 [json-iterator](https://github.com/json-iterator/go)，性能比原生的 encoding/json 快很多。

下面自己就简单测试了下两者的区别,测试代码很简单，就简单测试了下两者 Unmarshal 和 Marshal 的性能表现：
<!--more-->

测试代码: [json-benckmark_test.go](https://gist.github.com/javasgl/e405c436963d2fd7c91c03a05ddcb6b9)
<script src="https://gist.github.com/javasgl/e405c436963d2fd7c91c03a05ddcb6b9.js"></script>

执行 go test -test.bench=.后结果:
```
Benchmark_array_by_json-4        	  500000	      2748 ns/op	     456 B/op	      14 allocs/op
Benchmark_array_by_jsoniter-4    	 2000000	       676 ns/op	     144 B/op	       3 allocs/op
Benchmark_decode_by_json-4       	  500000	      2804 ns/op
Benchmark_decode_by_jsoniter-4   	 2000000	       764 ns/op
PASS
ok  	learnGo/json	7.217s
```
可见，json 和 jsoniter 在 Unmarshal 和 Marshal 方面的差别还是比较大的。

go 版本的 jsoniter/go 最近已经发布了 1.0 版本，Kubernetes V1.8 准备引入这个库,见 https://github.com/json-iterator/go/issues/154。

同时, jsoniter 也有 java 可以使用的库 [jsoniter/java](https://github.com/json-iterator/java), 据官方测试，性能比我之前比较喜欢使用的 alibaba 的 fastjson 都还要好,以后有机会可以尝试下。
