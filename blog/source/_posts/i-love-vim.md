---
title: I-love-vim
date: 2017-05-07 22:08:15
tags: [vim]
categories: tools
---

个人是一个`vi`重度使用者，所以尽可能的会希望所使用的工具中都能支持`vim`来方便操作。

- chrome浏览器支持 `vim`操作: Vimium 扩展
- atom 编辑器: vim-mode、vim-mode-plus
- sublime text: 天然支持，配置中`ignored_packages` 置为空`[]`即可
- idea 编辑器：`vim` 插件用来编辑文件内容，还有一个和`Vimium`相似的插件(名称遗忘,自己未使用)，以和`Vimium`相同的方式操作`idea`的各种菜单
- command line: 大部分天然支持`vim`编辑模式 (非 `windows` 系统),自行给`vim`安装了一些插件，打造成了一个命令行的`golang`的`IDE`
	- 目录：`scrooloose/nerdtree`
	- 目录git状态：`Xuyuanp/nerdtree-git-plugin`
	- 自动完成：`Shougo/neocomplete`
	- go相关格式等工具：`fatih/vim-go`

最后，附上一张命令行`IDE`效果图:
![vim-ide](images/vim.png)