---
title: 阅读 php 源码，ctags 配合 vim 实现代码跳转 
tags:
  - ctags
categories: notes
date: 2017-08-01 16:40:05
---


在阅读 php 源码的时候，为了方便跳转到相应的函数定义的位置，需要使用 ctags 来生成 tags 文件，再配合 vim 来实现跳转。


## 安装 ctags

macOS 上使用 ctags 非常简单，一般都预装了 ctags 。

如果没有安装，可以使用 `brew install ctags` 来安装。


## tags 文件

在 php 源码根目录，执行 `ctags -R `, 就会在当前目录下生成一个名为 `tags` 的 文件。 

```
~/codes/php-src(master*) » tail -n 20 tags                                                                                                                                       
zval_marked_grey	Zend/zend_gc.h	/^	uint32_t zval_marked_grey;$/;"	m	struct:_zend_gc_globals
zval_object_property_dump	ext/standard/var.c	/^static void zval_object_property_dump(zval *zv, zend_ulong index, zend_string *key, int level) \/* {{{ *\/$/;"	f	file:
zval_opt_copy_ctor	Zend/zend_variables.h	/^#define zval_opt_copy_ctor(/;"	d
zval_possible_root	Zend/zend_gc.h	/^	uint32_t zval_possible_root;$/;"	m	struct:_zend_gc_globals
zval_ptr_dtor	Zend/zend_execute.c	/^#define zval_ptr_dtor(/;"	d	file:
zval_ptr_dtor	Zend/zend_execute.c	/^#undef zval_ptr_dtor$/;"	d	file:
zval_ptr_dtor	Zend/zend_variables.h	/^#define zval_ptr_dtor(/;"	d
zval_ptr_dtor_nogc	Zend/zend_variables.h	/^#define zval_ptr_dtor_nogc(/;"	d
zval_ptr_dtor_wrapper	Zend/zend_variables.h	/^#define zval_ptr_dtor_wrapper /;"	d
```
<!--more-->
tags 文件的格式非常简单，以tab 分割各项，基本有以下几项内容:

第一列：tag name ,例如 `zval_ptr_dtor`

第二列：tag 所在的文件，例如 `Zend/zend_variables.h`

第三列：执行的命令，一般是指在 vi 中执行的正则搜索命令，例如 `/^#define zval_ptr_dtor(/;"` 

第四列：tag 的类型，例如：d , 类型在不同的语言中有不同的含义。在 c 中， d 代表着 macro definitions (宏定义)


具体的各语言的 tag 的类型定义可以使用 `ctags --list-kinds` 来查看:

```
~/codes/php-src(master*) » ctags --list-kinds
C
    c  classes
    d  macro definitions
    e  enumerators (values inside an enumeration)
    f  function definitions
    g  enumeration names
    l  local variables [off]
    m  class, struct, and union members
    n  namespaces
    p  function prototypes [off]
    s  structure names
    t  typedefs
    u  union names
    v  variable definitions
    x  external and forward variable declarations [off]
Java
    c  classes
    e  enum constants
    f  fields
    g  enum types
    i  interfaces
    l  local variables [off]
    m  methods
    p  packages
```

查看 ctags 支持哪些语言可以使用 `ctags --list-languages` 来查看:

```
~/codes/php-src(master*) » ctags --list-languages
....
C
C++
Flex
Fortran
HTML
Java
JavaScript
Lisp
Lua
Make
MatLab
OCaml
Pascal
Perl
PHP
Python
...
```
大概有 41 种，不过 并没有看到 对 go 的支持，应该是因为 go 语言还是比较年轻的原因。

## 使用 tags 

一般情况下，在 tags 文件所在目录使用 vi (vim) 打开源码文件，在文件中使用快捷键 `ctrl+]` 即可实现跳转，`ctrl+t` 即可回到跳转之前的位置。

如果无法跳转，提示 `E433: No tags file` ，则需要手动指定 tags 文件,通过 vim 命令或者直接修改 vim 的配置文件 vimrc 均可：

```
#在~/.vimrc中添加:
set tags+=~/codes/php-src/tags

#或者在vim中运行命令:
:set tags+=~/codes/php-src/tags

```

