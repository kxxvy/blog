---
title: 微信小程序使用canvas绘制圆角矩形在Android：变形、锯齿
date: 2021-04-23
tags: [微信小程序, canvas]
---

这两天用小程序实现生成微信二维码，用 iphone 手机测一直没问题，结果用 Android 测却发现画的图形始终缺个角，锯齿形的，最后查阅多方资料，发现使用 ctx.arc + ctx.lineTo 就能避免 Android 错误，Android 只使用 ctx.arcTo 不兼容, 真是个奇怪的八哥！！！

<!-- more -->

```js
/**
 * 绘制圆角矩形
 * @param {Object} ctx - canvas组件的绘图上下文
 * @param {Number} x - 矩形的x坐标
 * @param {Number} y - 矩形的y坐标
 * @param {Number} w - 矩形的宽度
 * @param {Number} h - 矩形的高度
 * @param {Number} r - 矩形的圆角半径
 * @param {String} [c = 'transparent'] - 矩形的填充色
 */
  roundRect: function(ctx, x, y, w, h, r, c = '#fff') {
    if (w < 2 * r) { r = w / 2; }
  if (h < 2 * r) { r = h / 2; }

  ctx.beginPath();
  ctx.fillStyle = c;

  ctx.arc(x + r, y + r, r, Math.PI, Math.PI * 1.5);
  ctx.moveTo(x + r, y);
  ctx.lineTo(x + w - r, y);
  ctx.lineTo(x + w, y + r);

  ctx.arc(x + w - r, y + r, r, Math.PI * 1.5, Math.PI * 2);
  ctx.lineTo(x + w, y + h - r);
  ctx.lineTo(x + w - r, y + h);

  ctx.arc(x + w - r, y + h - r, r, 0, Math.PI * 0.5);
  ctx.lineTo(x + r, y + h);
  ctx.lineTo(x, y + h - r);

  ctx.arc(x + r, y + h - r, r, Math.PI * 0.5, Math.PI);
  ctx.lineTo(x, y + r);
  ctx.lineTo(x + r, y);

  ctx.fill();
  ctx.closePath();
},
```
