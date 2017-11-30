---
title: Virtualenv 使用
date: 2017-11-30 21:25:33
tags: [python,virtualenv]
categories: [python]
---

一般情况下，大多数系统上安装的 python 版本为 2.x 的。但是有时候我们需要同时使用 python3.x 和 python2.x,这里推荐使用 virtualenv 来管理两个甚至是多个不同版本的 python。

## 安装 python3

mac系统安装python3,很简单， `brew install python3`, 升级也很简单,`brew upgrade python3`。注意可能需要 sudo。

## 安装 virtualenv

可以用过 pip来安装,`pip install virtualenv`

## 创建一个python环境

例如创建一个 python3 的环境，`virtualenv --python=python3 /usr/local/python3`

## 激活

执行`source /usr/local/python3/bin/activate`即可


