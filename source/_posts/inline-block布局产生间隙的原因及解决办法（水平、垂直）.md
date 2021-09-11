---
title: inline-block布局产生间隙的原因及解决办法（水平、垂直）
date: 2021-06-27
tags: [inline-block间隙问题]
---

## 起因

在做项目时碰到一个问题，发现inline-block之间存在间隙导致无法正确获取到底部滑块需要滑动的距离

<!-- more -->

## 探究

布局的一种方法是使用display:inline-block;它相比于与浮动、定位最大的不同就是其没有父元素的匿名包裹特性，这使得display:inline-block属性的使用非常自由，可与文字，图片混排，可内嵌block属性元素，可以置身于inline水平的元素中。但是在使用display:inline-block列表布局经常会遇到“换行符/空格间隙问题”。如下：

```html
<!DOCTYPE html>
<html lang="env">
  <head>
    <meta charset="UTF-8">
    <title>inline-block的空隙问题</title>
    <style>
      * {
        margin: 0;
        padding: 0;
      }
      .box {
        padding: 20px;
      }
      .main {
        display: inline-block;
        width: 100px;
        height: 100px;
        background-color: lightpink;
      }
    </style>
  </head>
  <body>
    <div class="box">
      <div class="main"></div>
      <div class="main"></div>
    </div>
  </body>
</html>
```

页面效果：

![zzz.png](https://i.loli.net/2021/09/11/PZr27HJ1I98RMby.png)

**原因**：inline-block元素间有空格或是换行，因此产生了间隙。

**解决办法：**

1.空格符本质上就是个字符，与a,b,c,d这些字符是个同一个属性的东西，只是他是空格，透明的看不见而已（但可以选中）。所以，只要我们使用让文字宽度为0的那些方法，就可以解决inline-block元素间换行符间隙的问题。

```css
{font-size: 0}
```

但是，这个方法在chrome浏览器下没有效果，在IE6/7下残留1像素间隙。

2.letter-spacing属性可以控制文字间的水平距离的，支持负值，可以让文字水平方向上重叠。
但是，基本上所有的浏览器对于不同字体下的空格符的水平占据的解析都是一致，唯一有瑕疵的是在Opera浏览器下，两个inline-block元素间空白间隙使用letter-spacing去除的极限是1像素，当看上去要正好为0的时候，letter-spacing似乎失效，空白间距恢复成letter-spacing:0时的效果。

**现在主要情况如下：**

+ block水平的元素inline-block化后，IE6/7没有换行符间隙问题，其他浏览器均有；

+ inline水平的元素inline-block后，所有主流浏览器都有换行符/空格间隙问题；

+ font-size:0，去除换行符间隙，在IE6/7下残留1像素间隙，Chrome浏览器无效，其他浏览器都完美去除；

+ letter-spacing负值可以去除所有浏览器的换行符间隙，但是，Opera浏览器下极限是间隙1像素，0像素会反弹，换行符间隙还原。

**将两个解决办法综合，就是一个完美的解决办法：**

给上述代码的父级块.box元素设置如下：

```css
.box {
  font-size: 0;
  letter-spacing: -3px;
}
```
