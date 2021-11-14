---
title: wx.showToast()一闪而过解决办法
date: 2021-10-19
tags: [微信小程序]
---

在写微信小程序项目时，

遇到一个奇怪的问题，

wx.showToast()这个 api 有时在使用时会一闪而过，

可明明是有设置 duration 时间的，

按理来说不应该会这样，

事出反常必有妖。

<!-- more -->

**原因**

经查阅相关资料，发现

`wx.showLoading()` 和 `wx.showToast()`只能同时显示一个，

`wx.showLoading` 与 `wx.showToast` 用的是同一个遮罩层

`wx.hideLoading()` 会关闭同级中的 `wx.showLoading` 或 `wx.showToast`

而我的项目正好在 `toast` 之前也有用到 `loading`，

恰好就也有这样的问题

所以要在 `showToast` 之前调用 `wx.hideLoading`
