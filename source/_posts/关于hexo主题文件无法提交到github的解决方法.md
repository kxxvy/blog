---
title: 关于hexo主题文件无法提交到github的解决方法
date: 2020-10-20
tags: [hexo]
---

写博客时偶然间发现主题文件无法上传至github

觉得挺奇怪的，随后便开始折腾寻找解决方法

<!-- more -->

## 一、遇到的问题

因为themes/yilia 也是从仓库里拉取下来的 它关联到了作者的git仓库 所以提交不上去

## 二、解决方法

1、从暂存区删除该文件夹

`bash git rm --cache themes/主题文件名` 比如我的是主题是 `yilia`

`bash git rm --cache themes/yilia`

2、接下来就可以愉快的提交上去了