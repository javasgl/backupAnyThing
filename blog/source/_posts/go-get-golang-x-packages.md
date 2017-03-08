---
title: go_get_golang_x_packages
date: 2017-03-08 18:56:51
tags: [go]
categories: go
---

go在go get 一些 package时候的会由于`众所周知`的原因而无法下载。比如在安装 `bee`的时候有可能会遇到无法下载 `golang.org/x/sys/unix` 的问题。
```
unrecognized import path "golang.org/x/sys/unix"
```

解决方案：手动从`github`下载相应的`package`

```
git clone --depth=1 https://github.com/golang/xxx.git
git clone --depth=1 https://github.com/golang/xxx.git

```
> 注：xxx 为对应的需要的库

下载完成后，`软链`或者`复制` 到 `$GOPATH/src/golang.org/x/` 下即可。
```
golang.org/
└── x
    ├── net
    ├── sys
    └── tools

4 directories
```
