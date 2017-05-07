---
title: hexo 命令备忘录
date: 2017-05-07 21:35:26
tags: [hexo,blog]
categories: notes
---

这里记录个人一些 `hexo` 常用命令备查:


- 创建新的文章: `hexo new post-title`
- 生成db和静态文件： `hexo generate`,缩写为 `hexo g`
- 删除cache和静态文件：`hexo clean`
- 本地开始预览server：`hexo server`,缩写为 `hexo s`,默认端口为 `4000`
- 部署到远程: `hexo deply`,缩写为 `hexo d`

有时候部署到远程后发现`blog`并没有更新，则可以先`clean`之后再 `deploy`:
```
hexo clean
hexo g -d
```