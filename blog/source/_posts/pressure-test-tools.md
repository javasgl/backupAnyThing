---
title: 压力测试工具ab、webbench、http_load、siege简单使用
date: 2017-06-05 10:05:14
tags: [ab,webbench,http_load,siege]
categories: [tools]
---
本文简单介绍ab、http_load、webbench、siege四种压力测试工具的使用。

# ab

`ab` 是 `apache` 服务器自带的一个压力测试工具。安装`apache` web服务器时就自动安装了。当然，`ab`也是可以单独安装的，如果你不想安装`apache`而只是想安装`ab`，那么可以使用以下方式安装:
```shell
[root@yourdream ~]# yum install httpd-tools
```
安装完成之后即可使用`ab`进行测试了。
```shell
[root@yourdream ~]# ab -h
Usage: ab [options] [http[s]://]hostname[:port]/path
Options are:
    -n requests     Number of requests to perform
    -c concurrency  Number of multiple requests to make
    ....
    -h              Display usage information (this message)
```
<!-- more -->

压力测试的时候一般使用到 `-n`和`-c`参数来分别指定运行次数和并发数:
```shell
[root@yourdream ~]# ab -n 1000 -c 100 https://baidu.com/
This is ApacheBench, Version 2.3 <$Revision: 655654 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking baidu.com (be patient)
Completed 100 requests
...
Completed 1000 requests
Finished 1000 requests

....

Concurrency Level:      100
Time taken for tests:   3.742 seconds
Complete requests:      1000
Failed requests:        0
Write errors:           0
...
Requests per second:    267.23 [#/sec] (mean)
Time per request:       374.213 [ms] (mean)
Time per request:       3.742 [ms] (mean, across all concurrent requests)
Transfer rate:          92.50 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:       93  260  50.7    269     498
Processing:    26   88  45.6     77     362
Waiting:       25   62  31.8     50     356
Total:        125  348  47.5    342     635

Percentage of the requests served within a certain time (ms)
  50%    342
  ....
  99%    534
 100%    635 (longest request)

```
测试结果中，重点关注`Requests per second`和`Time per request` 指标,分别是每秒请求数和单个请求耗时。注意这些指标只能作为性能参考，因为会受到各种因素的影响，比如网络环境的影响...

# http_load
`http_load`非常轻量，程序解压后`100kb`左右的大小。不过不支持直接测试`url`，需要先将要测试的`url`写入文件，把文件作为参数传递给`http_load`。
```shell
[root@yourdream ~]# http_load
usage:  http_load [-checksum] [-throttle] [-proxy host:port] [-verbose] [-timeout secs] [-sip sip_file]
            -parallel N | -rate N [-jitter]
            -fetches N | -seconds N
            url_file
One start specifier, either -parallel or -rate, is required.
One end specifier, either -fetches or -seconds, is required.
```
经常需要使用到的参数是`-parallel`和`-fetches`，分别表示并发数和总共请求数。
```shell
[root@yourdream ~]# http_load -parallel 100 -fetches 1000 a.txt
....
1000 fetches, 100 max parallel, 55088 bytes, in 1.8685 seconds
55.088 mean bytes/connection
535.189 fetches/sec, 29482.5 bytes/sec
msecs/connect: 31.5979 mean, 48.524 max, 26.395 min
msecs/first-response: 36.9429 mean, 1312.77 max, 5.107 min
999 bad byte counts
HTTP response codes:
  code 200 -- 514
```
测试结果中，重点关注`fetches/sec`和`msecs/connect`,分别表示每秒处理请求数和每个连接平均响应时间。

# webbench
`webbench` 也非常轻量级，和`http_load`大小差不多。
```shell
[root@yourdream ~]# webbench
webbench [option]... URL
  -f|--force               Don't wait for reply from server.
  -r|--reload              Send reload request - Pragma: no-cache.
  -t|--time <sec>          Run benchmark for <sec> seconds. Default 30.
  -p|--proxy <server:port> Use proxy server for request.
  -c|--clients <n>         Run <n> HTTP clients at once. Default one.
  -9|--http09              Use HTTP/0.9 style requests.
  -1|--http10              Use HTTP/1.0 protocol.
  -2|--http11              Use HTTP/1.1 protocol.
  --get                    Use GET request method.
  --head                   Use HEAD request method.
  --options                Use OPTIONS request method.
  --trace                  Use TRACE request method.
  -?|-h|--help             This information.
  -V|--version             Display program version.
```
`webbench`不支持指定总访问数，支持并发数(`-c`)和测试时长(`-t`)，不直接支持`https`测试。
```shell
[root@yourdream ~]# webbench -c 100 -t 10 http://baidu.com/
Webbench - Simple Web Benchmark 1.5
Copyright (c) Radim Kolar 1997-2004, GPL Open Source Software.

Benchmarking: GET http://baidu.com/
100 clients, running 10 sec.

Speed=14628 pages/min, 2448 bytes/sec.
Requests: 86 susceed, 2352 failed.
```
每秒钟响应请求数：146285 pages/min，每秒钟传输数据量:2448 bytes/sec

# siege
安装可以直接使用`yum`、`apt`安装，也自己去下载手动安装。
```shell
[root@yourdream ~]# siege
SIEGE 3.0.8
Usage: siege [options]
       siege [options] URL
       siege -g URL
Options:
  ...
  -h, --help                HELP, prints this section.
  ...
  -c, --concurrent=NUM      CONCURRENT users, default is 10
  -i, --internet            INTERNET user simulation, hits URLs randomly.
  ...
  -t, --time=NUMm           TIMED testing where "m" is modifier S, M, or H
                            ex: --time=1H, one hour test.
  ...

```
经常使用的参数是`-c`和`-t`，分别是并发数和测试时长。
```shell
[root@yourdream ~]# siege -c 100 -t 10S https://baidu.com
** SIEGE 3.0.8
** Preparing 100 concurrent users for battle.
The server is now under siege...
HTTP/1.1 200   0.03 secs:   28207 bytes ==> GET  /
HTTP/1.1 302   0.15 secs:     161 bytes ==> GET  /
....
Lifting the server siege..      done.

Transactions:		        1512 hits
Availability:		      100.00 %
Elapsed time:		        9.63 secs
Data transferred:	       20.38 MB
Response time:		        0.12 secs
Transaction rate:	      157.01 trans/sec
Throughput:		        2.12 MB/sec
Concurrency:		       18.14
Successful transactions:        1555
Failed transactions:	           0
Longest transaction:	        1.34
Shortest transaction:	        0.02
...
```
测试结果中，重点关注`Transaction rate`和`Concurrency`，分别表示每秒处理数和实际并发数。
`siege`可以将测试结果写入日志文件中，方便进行后续图形化工具或者分析工具使用。
```
        Date & Time,  Trans,  Elap Time,  Data Trans,  Resp Time,  Trans Rate,  Throughput,  Concurrent,    OKAY,   Failed
2017-06-05 11:38:02,   1512,       9.63,          20,       0.12,      157.01,        2.08,       18.14,    1555,       0
```
