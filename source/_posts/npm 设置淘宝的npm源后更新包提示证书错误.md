---
title: npm 设置淘宝的npm源后更新包提示证书错误
date: 2022-09-18
tags: [npm]
---

## 一、背景

设置 `npm` 源为 `registry.npm.taobao.org` 更新部分包时提示证书错误

<!-- more -->

## 二、原因

这是因为 `ssl` 验证问题，使用下面的命令取消 `ssl` 验证即可解决

## 三、解决方案

```zsh
npm config set strict-ssl false
```
