---
title: go覆盖测试率图形化分析
date: 2018-05-23 11:36:53
tags: [go,UnitTest]
categories: go
---

go test可以用来做单元测试，-cover参数可以显示测试覆盖率。
```
go test -v ./... -cover
```
结果如下：
```
...
PASS
coverage: 34.4% of statements
```
但是有时候需要提高覆盖测试率，就需要知道哪些代码被覆盖到了，而哪些代码没有被覆盖到。这时候可以借助go提供的一个工具来实现。

<!--more-->

- 首先，执行单元测试时，添加 `-coverprofile=profileName`,生成 profile文件。

```
go test -v ./... -coverprofile=aaaa

```
- 之后，使用`go tool cover -html=profileName -o coverage.html`来生成html文件后用浏览器打开。

```
go tool cover -html=aaaa -o coverage.html
open -a /Applications/Google\ Chrome.app coverage.html
```
最终看到的效果是：
![coverage](/images/coverage.png)
红色表示没有覆盖到的代码，绿色表示覆盖到的代码，这样就能针对性的编写单元测试，提高测试覆盖率了。

