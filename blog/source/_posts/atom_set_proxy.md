---
title: Atom设置代理
date: 2017-03-07 22:10:01
tags: [atom,proxy]
categories: tools
---

由于`众所周知`的原因，Atom有时候安装插件的时候很慢甚至无法安装。可以通过设置代理来解决。
前提是已经有一个可以使用的http代理。[参考之前的文章：将socks5转为http代理](/transfer_socks5_to_http_proxy)

- atom自带了一个工具 `apm`(atom package management) 这个工具可以用来设置一些系统配置

- 可以用`apm config list` 查看现有的系统配置信息

下面开始设置 http代理：
<!--more-->

- 执行一下命令

```
apm config set http-proxy http://host:port
apm config set https-proxy http://host:port
apm config set strict-ssl false

```
- 注意两点：
	- https 配置的也是 http 而不是 https!
	- set strict-ssl false 可以避免ssl 证书错误问题

> 其实直接修改 ~/.atom/.apmrc 文件也是可行的

具体说明可以查看 [这里](https://github.com/atom/apm#behind-a-firewall)
