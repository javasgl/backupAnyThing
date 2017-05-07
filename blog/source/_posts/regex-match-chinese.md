---
title: 中文正则匹配
date: 2017-05-07 20:36:05
tags: [regex,python]
categories: notes
---

在某些场景下，需要使用正则对文本中的中文进行匹配。之前就在一个`python`的项目中需要对中文进行相应的匹配。
实际需求: 要求匹配非中文内容。
中文的`unicode`编码范围为 `u4e00` 到 `u9fa5`。
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

import re

group = re.search(ur'([^\u4e00-\u9fa5])','1中文en汉字')

if group:
	print grou.groups()

```

Tips:

> 1.正则非匹配: ^
> 2.正则范围匹配: [a-b]
> 3.正则捕获: ()
> 4.python2 中 u 表示 `unicode`的字符串, python3 已经全部使用`unicode`来表示字符串
> 5.python 中 r 表示 `row string`,使用原始字符串，不进行转义



