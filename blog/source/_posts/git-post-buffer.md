---
title: git 推送大文件buffer设置
date: 2017-11-15 23:09:50
tags: [git]
categories: [tools]
---

git默认的 http.postBuffer大小为1M。所以偶尔遇到比较大的文件或者项目时，会报出以下错误：
```
error: RPC failed; result=22, HTTP code = 411
fatal: The remote end hung up unexpectedly
fatal: The remote end hung up unexpectedly
```

这时可以通过设置 http.postBuffer来解决。下面设置为100M。
```
git config --global http.postBuffer=102400
```