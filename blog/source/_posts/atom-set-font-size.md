---
title: Atom 调整左侧目录字体大小
date: 2017-03-07 22:39:49
tags: [atom]
categories: tools
---

刚刚安装好 atom的时候， 左侧的 tree 目录字体特别小，可以通过一下方式来设置

- 打开`Atom`-`Stylesheet`

- 找到 `.tree-view`
```
// style the background color of the tree view
.tree-view {
  // background-color: whitesmoke;
}
```
- 给 `.tree-view` 添加 `font-size` 即可：
```
.tree-view {
    font-size:16px;
}
```
- 当然，添加其他属性也是可以的，可以自己定制样式，颜色，字体，字号等，`CSS`语法
