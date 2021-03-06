---
title: python的浅复制和深复制
date: 2018-03-30 18:54:49
tags: [python,shallowcopy,deepcopy]
categories: [python]
---

最近使用python过程中遇到了一个看似诡异的问题,由于惯性思维，这个问题花了一点时间一步步调试才找到。
先来看看下面的代码的结果：
```
a = [1,2,3]
b = a
b.append(4)
print(b)
print(a)
```
如果这段代码放在其他语言,Java 或者 PHP 中，结果大多是这样的：
```
[1,2,3,4]
[1,2,3]
```
但是在 python 中，实际执行的结果却是这样的：
```
[1,2,3,4]
[1,2,3,4]
```
修改 b 的值，却影响了 a 的值。这个就是 python 和其他语言有一点不一样的地方了，简单可以理解为：python 没有赋值，只有引用。
如果以其他语言的方式来理解，那就是 a,b 都是指向同一块内存数据的地址，所以修改其中任何一个，都会引起彼此的变化，因为底层数据都是同一份。

现在，如果需要这段代码表现的和其他语言一致，该怎么写呢？

<!--more-->

### 浅复制(shallow copy)

对于list 列表，python 有 copy 方法：
```
a = [1,2,3]
b = a.copy()
b.append(4)
print(b)
print(a)
```
代码执行结果就基本能达到预期了：
```
[1,2,3,4]
[1,2,3]
```
### 深复制(deep coopy)

但是需要注意的是，这个 copy 方法是浅复制(shallow copy),那么它对于嵌套对象就无能为力了。
如果需要复制嵌套对象，可以使用 import copy ,然后使用 `copy.deepcoopy()`来实现对嵌套对象的复制
```python
import copy
a = [1,2,3,[4,5]]
# b = a.copy()
b = copy.deepcopy(a)
b.append(7)
b[3][1]=6
print(b)
print(a)
```
代码执行结果是这样的：
```
[1, 2, 3, [4, 6],7]
[1, 2, 3, [4, 5]]
```
可以看到 a 并没有因为 b 对嵌套内容的修改而改变。这里需要注意的一点是，如果修改的不是嵌套的内容，那么普通的copy也还是可以的，
此例中，append(7) 时，使用 `copy` 或者 `copy.deepcopy` 的效果是一样的，a的值都不会受到影响。

deepcopy的本质是递归复制。

对于 list 数据结构而言，可以用 list(x) 来代替 copy, 不过它依然是浅复制,这点需要注意。
