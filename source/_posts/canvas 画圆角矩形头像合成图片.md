---
title: canvas 画圆角矩形头像合成图片
date: 2021-08-10
tags: [canvas]
---

在项目中遇到一个需求，要在canvas上绘制一张图片，为了显示好看一点，需要给该图片再绘制一个背景

<!-- more -->

**画圆形头像方法**

```js
function drawRound (ctx,r,x,y,img) {
  ctx.save() // 保存之前的
  var r = r // 半径*屏幕分辨率比例
  var d = 2*r // 直径
  var cx = x + r // 圆弧坐标x
  var cy = y + r // 圆弧坐标 y
  ctx.arc(cx, cy, r ,0, 2*Math.PI)
  ctx.clip() // 裁剪
  ctx.drawImage(img, x, y, d, d) // 画头像
  ctx.restore() // 返回上一状态
}
```

**画圆角矩形头像方法**

```js
function drawRoundRect (ctx,r,x,y,w,h,img) {
  ctx.save()
  if (w < 2 * r) r = w / 2
  if (h < 2 * r) r = h / 2
  ctx.beginPath()
  ctx.moveTo(x+r, y)
  ctx.arcTo(x+w, y, x+w, y+h, r)
  ctx.arcTo(x+w, y+h, x, y+h, r)
  ctx.arcTo(x, y+h, x, y, r)
  ctx.arcTo(x, y, x+w, y, r)
  ctx.closePath();
  ctx.clip()
  ctx.drawImage(img, x, y, w, h)
  ctx.restore() // 返回上一状态
}
```

**导出生成头像链接**

导出的数据为base64的图片链接，可以赋值到需要展示的图片上，这样就能实现图片合成的效果了

```js
var imgData = canvas.toDataURL()
```
