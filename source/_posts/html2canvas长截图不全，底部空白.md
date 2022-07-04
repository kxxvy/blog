---
title: html2canvas长截图不全，底部空白
date: 2022-07-04
tags: [html2canvas]
---

## 一、前言

最近在做项目时，有截长图的需求，

然后我就像往常一样使用`html2canvas`插件做长截图，

但是意料之外的是，

截图上半部分显示完好，

底部却大片内容空白，

<!-- more -->

## 二、问题处理

而后经过查阅[html2canvas](https://github.com/niklasvh/html2canvas)文档，

以及经过多次测试和排查，

发现`html2canvas`截取图片是有最大限制的，

所以在截取长图的时候会因为内容过长而截取不全，

注意到`html2canvas`提供了一个`scale`配置属性，

该属性表示[Window.devicePixelRatio](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/devicePixelRatio)

`html2canvas`默认的缩放比例为2，

我们可以把它适当调小点，设置为1，

通过牺牲清晰度的方式获取更大的截图内容，

问题得以解决！
