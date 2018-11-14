---
title: js 回调函数访问上下文 this 对象
date: 2017-05-27 18:10:14
tags: ["js","es6"]
categories: ["javascript"]
---
在开发项目之中，使用到js ajax 同服务端进行数据交互。一般图方便直接使用`jquery`或者`axios`等已有的库。
一般代码如下:
```js
//axios
axios.get('/url').then(function(res){
  console.log(res)
})
//juqery
$.get('/url',function(res){
  console.log(res)
},'json')
```
那么经常这么使用的肯定会遇到一个场景，就是:在回调函数中如何访问上下文中的`this`对象?
<!--more-->
```js
function request(){
  //this
  axios.get('/url').then(function(res){   
    //in callbacl ,how to access this
  })
}
```
之前的做法是这样的:将`this`赋值给一个局部变量,然后在回调函数中访问这个变量
```js
function request(){
  //this
  var that = this;
  axios.get('/url').then(function(res){   
    //in callbacl ,how to access this
    console.log(that)
  })
}
```
后来知道了更为优雅的两种方式:

使用 `bind`函数:
```js
//axios
function request(){
  axios.get('/url').then(function(res){
    console.log(this);
  }.bind(this));
}
//jquery
function request(){
  $.get('/url',function(res){
    console.log(this);
  }.bind(this),'json');
}
```
使用`ES6`的箭头函数:
```js
//axios
function request(){
  axios.get('/url').then((res)=>{
    console.log(this);
  ));
}
//juqery
function request(){
  $.get('/url',(res)=>{
    console.log(this);
  },'json');
}
```
参考:

[深入浅出ES6（七）：箭头函数 Arrow Functions](!http://www.infoq.com/cn/articles/es6-in-depth-arrow-functions)
