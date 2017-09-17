---
title: http压力测试工具--wrk
date: 2017-09-17 10:36:55
tags: [http,benchmark,wrk]
categories: [tools]
---


发现一个新的 http 压力测试工具 wrk ,简单好用。下载地址：https://github.com/wg/wrk 。类似于之前个人经常使用的一些压测工具，详细请移步 [压力测试工具ab、webbench、http_load、siege简单使用](/pressure-test-tools)。

wrk 使用非常简单，参数不多，但是足够自己简单压测一些 http 服务了。下面简单介绍下如何安装和使用。

<!--more-->

## 安装

需要自己把源码下载下来，自行编译。

```js
git clone --depth=1 https://github.com/wg/wrk
cd wrk
make
```
编译完成之后，会在当前目录下生成一个 `wrk`的可执行文件。为方便以后使用，可以将其复制到系统 path 路径中：
```js
cp wrk /usr/local/bin/.
```

## 使用

使用 wrk 会提示使用方式：

```js
~ » wrk
Usage: wrk <options> <url>
  Options:
    -c, --connections <N>  Connections to keep open
    -d, --duration    <T>  Duration of test
    -t, --threads     <N>  Number of threads to use

    -s, --script      <S>  Load Lua script file
    -H, --header      <H>  Add header to request
        --latency          Print latency statistics
        --timeout     <T>  Socket/request timeout
    -v, --version          Print version details

  Numeric arguments may include a SI unit (1k, 1M, 1G)
  Time arguments may include a time unit (2s, 2m, 2h)
```
使用案例：
```js
wrk -c300 -d10m -t8 http://google.com
```

-c300 ： 300连接
-d10m :  测试10分钟
-t8	  :  使用8个线程

测试结果如下：
```js
~ » wrk -c300 -d5s -t8 http://baidu.com
Running 5s test @ http://baidu.com
  8 threads and 300 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   474.98ms  177.40ms   1.44s    77.13%
    Req/Sec    47.61     41.14   180.00     79.78%
  516 requests in 5.05s, 194.51KB read
  Socket errors: connect 0, read 2357, write 0, timeout 0
Requests/sec:    102.18
Transfer/sec:     38.52KB
```

