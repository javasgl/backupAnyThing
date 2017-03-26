---
title: Alfred 编写 workflow
date: 2017-03-26 22:41:37
tags: [Alfred,workflow]
categories: [tools]
---
`Alfred` 的一个亮点功能就是`workflow`。通过`workflow`用户可以很容易的扩展`alfred`的功能,
而开源的优秀的`workflow`也有很多，不过如果找不到自己满意的`workflow`，可以自己动手编写`workflow`。自己动手，丰衣足食！
之所以重复造轮子的原因的是因为这个[datetie-format-converter](https://github.com/mwaterfall/alfred-datetime-format-converter) 转换的时间是按照UTC-GMT时间来算的，所以对于中国来说，属于东八区，所以就写了一个`php`版本的自己使用。
这里只是简单记录下自己开发的一个`Unix`时间戳转换的`workflow`的过程，详细教程请自行`google`或者参考 [这篇教程](http://myg0u.com/python/2015/05/23/tutorial-alfred-workflow.html).
<!--more-->
### 一些基本概念
`workflow`可以使用脚本语言来编写，包括但不限于`bash`、`php`、`python`、`ruby`等。`workflow`中的`triggers`、`inputs`、`actions`、`ouputs`等基本概念就不解释了，详细了解的可以自己一个个的试一试，反正总共加起来也不多。
### Alfred XML格式数据
`Alfred`中要求`ouputs`最后输出特定`Schema`的 `XML`内容。其实这一点我不太理解为什么，个人一直不喜欢`XML`格式的数据，重复臃肿、层级难于管理，就数据交换格式而言，个人比较倾向简单清晰易读的`JSON`，`Alfred`不支持返回`JSON`数据结果，不知道是不是由于`MacOS`的原因，没有深究。
`Alfred`要求输出的内容以下：
```xml
<items>
    <item autocomplete = "autocompletex" uid = "123321" arg = "argsx" >
    <title >title</title>
    <subtitle >subtitle</subtitle>
    <icon >icon</icon>
    </item>
</items>
```
根节点为items 其中包括任意多个item节点，每一个item节点代表本次查询结果的一行。 每一个item节点包括若干parameter与childnode，其含义为： uid： 每一个item要有一个独立的uid，不可重复 valid：值为yes 或者 no, 若为no，该行结果不可被选择 autocomplete ： 自动补全的值， 使用tab可以令alfred自动补全为 autocompelete属性的值. arg：作为下一个模块的参数传递

<title> 该行item的标题，也是主要显示的位置。 <subtitle> 该行字标题位置，会被显示为灰色小字 <icon> 该行图标的文件名，其大小为64X64 pixels
### 接受参数
`Alfred`可以通过`query`字段来获取数据的内容。
### 逻辑处理&格式输出
获取`query`的内容后，使用熟悉的脚本语言进行一些处理后组成转符合上面`Schema`的`XML`数据格式直接输出即可。
### 调试
`Alfred`很人性化的为开发者提供了调试工具，在`workflow`开发界面的右上角`Toggle debuggind mode`即可在下方打开一个`debug`窗口输出一些调试信息，方便开发。
### 说明
以下是开发的一个简单的时间戳转换`workflow`,代码没有做严格的输入检查，只是简单的将输入的内容通过`php`的`date`函数转换成人类可读的`Y-m-d H:i:s`格式.
```php
date_default_timezone_set('Asia/Shanghai');
require_once ('workflows.php');
$query = "{query}";
$wf = new Workflows ();
$wf->result ( time (), $query, date('Y-m-d H:i:s',$query), '', 'icon.png', 'yes' );
echo $wf->toxml();
```
其中`worklows.php` 中就只用到了 输出 `XML` 的方法，这个方法就是将`resutl()`中传递的参数组装成特定`XML`数据。
### 后续
后来研究了下[datetime-format-converter](https://github.com/mwaterfall/alfred-datetime-format-converter)的源码，做了简单的修改，已经支持北京时间的转换，其实就是在作者的`python`代码中时间转换的时候加上了时区的设置，手动将时区设置为`Asia/Shanghai`即可。
```python
def parse_query_value(query_str):
    """ Return value for the query string """
    try:
        query_str = str(query_str).strip('"\' ')
        if query_str == 'now':
            d = utcnow().shift('Asia/Shanghai')
        else:
            # Parse datetime string or timestamp
            try:
                d = epoch(float(query_str)).shift("Asia/Shanghai")
            except ValueError:
                d = parse(str(query_str)).shift('Asia/Shanghai')
    except (TypeError, ValueError):
        d = None
    return d
```
将第`6`、`10`、`12`行中时间转换加上了`shift('Asia/Shanghai')`
```python
d = epoch(float(query_str))
```
改为
```python
d = epoch(float(query_str)).shift("Asia/Shanghai")
```
即可。其实更好的做法是自动检测系统信息推断出用户所使用的时区并转换。

### 参考资料
- http://myg0u.com/python/2015/05/23/tutorial-alfred-workflow.html
- http://www.deanishe.net/alfred-workflow/
- http://www.deanishe.net/alfred-workflow/tutorial.html

