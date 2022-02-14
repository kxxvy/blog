---
title: this.getOptions is not a function
date: 2022-01-13
tags: [sass]
---

记录一个十分常见的关于sass的报错问题

## 一、 出现问题场景

在安装完sass-loader和node-sass之后，

vue运行项目过程中控制台报错`this.getOptions is not a function`，

经检查代码中并无写错的地方，

经查阅多方资料，发现其实是版本问题

当前安装的`scss-loader`版本太高，

需卸载当前版本安装低版本即可。

<!-- more -->

## 二、解决方案

```bash
npm uninstall --save sass-loader  # 卸载
npm uninstall --save node-sass  # 卸载
npm i -D sass-loader@8.x  # 安装
npm i -D node-sass@4.14.1  # 安装
```
