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

解决方案：
```
/Applications/Google Chrome.app/Contents/MacOS/Google Chrome --explicitly-allowed-ports=6666,8888
```
或者换端口，避开Chrome的非安全端口限制。
参考资料：
- https://jazzy.id.au/2012/08/23/why_does_chrome_consider_some_ports_unsafe.html
- https://support.google.com/chrome/forum/AAAAP1KN0B0l5d-nXEjLMM?hl=en
<!--more-->
Chrome 非安全端口列表如下：
```
  1,    // tcpmux
  7,    // echo
  9,    // discard
  11,   // systat
  13,   // daytime
  15,   // netstat
  17,   // qotd
  19,   // chargen
  20,   // ftp data
  21,   // ftp access
  22,   // ssh
  23,   // telnet
  25,   // smtp
  37,   // time
  42,   // name
  43,   // nicname
  53,   // domain
  77,   // priv-rjs
  79,   // finger
  87,   // ttylink
  95,   // supdup
  101,  // hostriame
  102,  // iso-tsap
  103,  // gppitnp
  104,  // acr-nema
  109,  // pop2
  110,  // pop3
  111,  // sunrpc
  113,  // auth
  115,  // sftp
  117,  // uucp-path
  119,  // nntp
  123,  // NTP
  135,  // loc-srv /epmap
  139,  // netbios
  143,  // imap2
  179,  // BGP
  389,  // ldap
  465,  // smtp+ssl
  512,  // print / exec
  513,  // login
  514,  // shell
  515,  // printer
  526,  // tempo
  530,  // courier
  531,  // chat
  532,  // netnews
  540,  // uucp
  556,  // remotefs
  563,  // nntp+ssl
  587,  // stmp?
  601,  // ??
  636,  // ldap+ssl
  993,  // ldap+ssl
  995,  // pop3+ssl
  2049, // nfs
  3659, // apple-sasl / PasswordServer
  4045, // lockd
  6000, // X11
  6665, // Alternate IRC [Apple addition]
  6666, // Alternate IRC [Apple addition]
  6667, // Standard IRC [Apple addition]
  6668, // Alternate IRC [Apple addition]
  6669, // Alternate IRC [Apple addition]
```

