---
title: Chrome 非安全端口限制
date: 2017-08-19 23:30:32
tags: [chrome,network]
categories: [notes]
---

在 beego 群里，有同学问了一个看似比较的简单的问题。问题是这样的: 一个beego web项目，将项目的端口号从默认的 8080 改为 6666 之后通过 Chrome 浏览器无法访问，但是通过其他浏览器却可以访问。

但是并未在意，认为这可能是chrome浏览器或者系统不小心配置了代理导致的，后来我自己在本机上也实验以下，将应用端口改为 6666 之后，通过 Chrome 浏览器也无法访问。报错信息如下:
```
This site can’t be reached

The webpage at http://localhost:6666/ might be temporarily down or it may have moved permanently to a new web address.
ERR_UNSAFE_PORT
```
报错信息中提到了 `ERR_UNSAFE_PORT`。