---
title: angular ng-repeat 双向数据绑定实现动态增删DOM
date: 2018-11-14 11:44:50
tags: [angular,js,ng-repeat,mvvm,ng-model]
categories: ["javascript"]
---
在实现一个需要动态增删的form表单需求时，`MVVM`的思维模式下，很容易就想到使用数组的添加和删除来实现DOM元素的动态添加和删除。

<!-- more -->
### 代码
首先定义一个空数组,`push`几条初始数据(或者在相关按钮的`click`事件中去对这个数组进行增删)，然后在页面上使用`ng-repeat`和`ng-model`在循环中绑定这个数组的数据。
```js
item= {
	name:'default value'
}
$scope.items= []
$scope.items.push(item)
$scope.items.push(item)
```
html页面如下：
```html
<div ng-repeat="item in items track by $index">
	<input type="text" ng-model="item.name">
</div>

```
### 问题
以上代码看似没有问题，但是实际结果却并不预期。页面渲染完成后，修改两个`input`中的任何一个，数组中的两个元素的数据都保持了一致变动。尝试了一下几种方案之后均无法实现单独绑定:
```html
方案1：
<div ng-repeat="item in items track by $index">
	<input type="text" ng-model="items[$index].name">
</div>

方案2：
<div ng-repeat="item in items track by $index">
	<input type="text" ng-model="$parent.items[$index].name">
</div>
```
后来怀疑是代码中`push(item)`的问题，每次`push`的时候，添加的都是同一个`item`,这样数组中的每一个元素引用的都是内存中的同一个`item`的地址，所以修改任何元素，都会影响内存中的同一个`item`对象，从而导致所有的数组元素的值都同步一致修改了。

### 解决
基于以上思路，产生了两种解决方案：
方案一，每次`push`的时候，添加一个空对象到数组中，这样避免每个元素引用同一个内存对象
```js
$scope.items.push({})
$scope.items.push({})
$scope.items.push({})
......
```
方案二，每次`push`的时候，使用 `angular.copy`重新复制出一个新对象出来添加到数组中
```js
item= {
	name:'default value'
}
$scope.items.push(angular.copy(item))
$scope.items.push(angular.copy(item))
$scope.items.push(angular.copy(item))
......
```