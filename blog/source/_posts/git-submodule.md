---
title: 修改 git submodule
date: 2020-03-22 17:24:51
tags: [git,submodule]
categories: [git]
---

## 初始
`git` 项目中，可以使用以下命令为项目添加`submodule`

```
git submodule add -b branch_name git@github.com:javasgl/xxx.git module_path
```

执行以上命令后，会在当前项目下生成一个`.gitmodules` 文件，内容如下

```
[submodule "module_path"]
	path = module_path
	url = git@github.com:javasgl/xxx.git
	branch = branch_name

```

同时也将修改 `.git/config`文件
```
[submodule "module_path"]
	url = git@github.com:javasgl/xxx.git
	active = true

```

此时，执行以下命令即可从远程拉取相关代码到`submodule`中:
```
git submodule update --init

// 可以使用 git submodule deinit 重新初始化
```
执行之后，会在 `.git/modules/module_path/` 下看到 `submodeule`相关的代码

```
.git/modules
└── module_path
```

## 变更
有时候，对于已经添加了submodule的项目，如果想更换 `submodule` 的地址，那个这就比较麻烦，目前`git`并没有直接提供相关命令,需要进行一系列的手工操作

简单记录如下，备查

比如，想把 `submodule` 的地址换为 `github.com/xxx/xxx.git`

1. 将 `submodule` 移除 `git` 版本控制
```
git rm module_path
```

2. 移除 `.git/config` `.gitmodules` 中的配置

3. 删除 `.git/modules/`下相关代码
```
rm .git/modules/module_path
```

4. 执行之前的初始化，可以重新`add`新的`submodule`
```
git submodule add github.com/xxx/xxx.git xxx_path
```
