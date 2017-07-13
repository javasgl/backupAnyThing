---
title: Mac上使用Privoxy 将 socks5转换为 http 代理
date: 2017-03-06 23:07:20
tags: [proxy,socks5,http,privoxy,shadowsocks]
categories: tools
---

shadowsocks 挺不错的，但是有些时候需要使用http代理来爬墙。这时候可以使用privoxy来将 socks5 代理转换为 http代理。
## 配置

步骤如下：

- 首先，确保 shadowsocks 已经正常起来的，默认的本地socks5端口号为 1080,可以使用 netstat 和 lsof 命令查看端口情况。

- 安装privoxy, mac 使用 brew install privoxy 即可

- 安装完成后，修改privoxy配置文件
<!--more-->
```
vim /usr/local/etc/privoxy/config
```
- 修改内容如下：
```
forward-socks5t   /               127.0.0.1:1080 .
listen-address  127.0.0.1:8118
```
`listen-address` 默认是监听本地8118端口，如果端口没有被占用，可以不用修改
- 启动privoxy
```
/usr/local/sbin/privoxy /usr/local/etc/privoxy/config
```
可以使用 `ps aux|grep privoxy `和 `lsof -i:8118`来检查是否成功启动
- 正常情况下,可以使用http代理了，代理地址`http://127.0.0.1:8118`

## 示例

例如，可以给 git 设置 http 或 https 代理,设置方式如下：
```js
git config --global http.proxy http://127.0.0.1:8118
git config --global https.proxy http://127.0.0.1:8118
```
取消代理配置：
```js
git config --global --unset http.proxy
git config --global --unset https.proxy
```
和其他 git 的配置一样，不使用命令行而是直接修改相应的 `.gitconfig` 文件也是可以的。
