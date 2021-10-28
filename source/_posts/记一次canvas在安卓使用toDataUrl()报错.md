---
title: 记一次canvas在安卓使用toDataUrl()报错
date: 2021-09-26
tags: [canvas]
---

最近线上反馈了一个奇怪的 bug，

该功能在 iphone 机型上能正常运行，

在部分安卓机上却会报 toDataUrl()错误。

<!-- more -->

找了很久始终还是没有找到为什么会出现这种 bug，

最后看到一个方法说把 img.src 放到 img.onLoad 后面，

尽然就奇迹般的跑起来了，

真是神奇，

至今还没找到个所以然。
