---
title: 解决MacOS Sierra 升级后原有的ssh private key无法通过验证
date: 2017-03-19 13:36:32
tags: ['mac','ssh']
categories: os
---

周末正好有空,就准备把`mac pro` 升级到最新的`sierra`版本，毕竟已经落后于`apple`官方两个比较大的版本了。

升级过程很简单，也很顺利。直接通过`appstore`上在线更新即可，下载完成之后安装向导步骤完成即可。

直到....我发现公司测试环境`502`之后，想要登陆到机器上去处理一下的时候，我的`ssh key`竟然毫无征兆的被deny了！升级之前能登陆服务器，升级之后无法登陆。

<!--more-->

问了其他同事，跳板机能正常登陆的，排除了机房出问题的可能性。那就说明这是本地的`ssh agent`升级后出问题了。

同时，我也试了下`github`的`key`是否也不能使用。结果正如我所料:
```java
ssh -v github.com
.....
.....
debug1: Skipping ssh-dss key xxxx/xxxx/xxx/ - not in PubkeyAcceptedKeyTypes
.....
Permission denied (publickey).
```
更加证实了我的想法: `这是Mac升级后导致的`
后来 `google` 发现这个问题很正常，很多人都在升级后遇到了这个问题。同时也暂时采用了临时方案：
```java
Host "github.com"
    User "git"
    IdentityFile "/xxx/xxxx/xxx"
    PubkeyAcceptedKeyTypes=+ssh-dss
```
在`.ssh/config` 中添加了 `PubkeyAcceptedKeyTypes=+ssh-dss` 配置，让 ssh 接受 `dsa` 类型的`key`。

最终方案应该是采用更加安全的 `RSA` 算法来生成 `key`，长度为 `2048`的 `RSA`。`1024`长度的`RSA`已经有被暴力破解成功的记录了，所以建议采用 `2048` 长度
```java
ssh-keygen -t rsa -b 2048 -C "new key"
```

当然，采用 `ecdsa` 更好，不过考虑到`Openssl`的兼容问题，在`ecdsa`未完全通用的情况下还是使用`RSA`比较好。
