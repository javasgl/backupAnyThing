---
title: 可变参数使用
date: 2017-08-30 09:11:38
tags: [go,php,java,python]
categories: [go]
---

很多语言都支持可变参数，下面记录下平时主要使用的几种语言的可变参数用法。主要有 go、php、python、java、js等语言的可变参数的基本使用。

<!--more-->

## go 可变参数

go 可变参数只能是最后一个参数，参数接收后存放在一个 slice 中。
```
package main

import (
	"fmt"
	"reflect"
)

func main() {

	hello("variable parameter", 1, 3, 4, 45, 56, 3)
	hello("second", []int{22, 33, 44}...)
}

func hello(b string, args ...int) {
	fmt.Println(b)
	fmt.Println(args)
	fmt.Println(reflect.TypeOf(args))
}

```
输出:
```
variable parameter
[1 3 4 45 56 3]
[]int
second
[22 33 44]
[]int
```

## php 可变参数
可变参数也只能是最后一个参数,参数接收后存放在一个 array 中。
```
function hello( $a , ...$args )
{
	print_r( $a );
	echo PHP_EOL . '.....' . PHP_EOL;
	print_r( $args );
}

hello( 'first' , 1 , 2 , 'string' , 4 , "5" );
hello( 'second' , ...[ 22 , 33 , 44 , 55 ] );
```
输出：
```
first
.....
Array
(
    [0] => 1
    [1] => 2
    [2] => string
    [3] => 4
    [4] => 5
)
second
.....
Array
(
    [0] => 22
    [1] => 33
    [2] => 44
    [3] => 55
)
```

## python 可变参数
python的可变参数有两个种方式

```
#!/usr/bin/env python
def hello(a,b,*c,**kwargs):
    print a,b,c,kwargs

hello("first","second")
hello("first","second","third","fourth",order="asc",group="admin")
```
输出：
```
first second () {}
first second ('third', 'fourth') {'group': 'admin', 'order': 'asc'}
```

python 中可变参数方式一：*arg,这种可变参数传递时不需要指定参数的key，参数接收后存放在 tuple 中。
python 中可变参数方式二：**kwargs，这种方式接收的参数需要指定参数的key，参数接收后存放在 dict 中。

## java 可变参数

Java 的可变参数只有能有一个，并且必须是方法的最后一个参数。
```
public class Hello{

  public static void main(String[] args) {
      hello("first",2,"args1","args2");
  }

  public static void hello(String a,int b,String... args){
    System.out.println(a);
    System.out.println(b);
    for(String param :args){
      System.out.println(param);
    }
  }
}

```
输出：
```
first
2
args1
args2

```
可变参数存放于数组之中。

## js 可变参数
js 可变参数可以通过js内置的 arguments 来访问：
```
function hello(a){
	console.log('a:'+a);
	for(i in arguments){
		console.log(arguments[i]);
	}
}
hello("first",1,2,'string')
```
输出:
```
a:first
first
1
2
string
```
arguments 中也包含了其他非可变参数的内容。



