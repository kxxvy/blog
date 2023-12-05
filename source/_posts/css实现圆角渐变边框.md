---
title: css实现圆角渐变边框
date: 2023-03-25
tags: [css]
---

## 前言

我们公司的 UI 在项目中做了个圆角渐变边框的设计

使用 css 感觉还不好实现

特此整理记录一下所有的实现方案

<!-- more -->

## 方案一：border-image 实现渐变边框

```html
<div class="border-image"></div>
```

```css
.border-image {
  width: 200px;
  height: 100px;
  border-radius: 10px;
  border-image-source: linear-gradient(45deg, gold, deeppink);
  border-image-slice: 1;
  border-image-repeat: stretch;
}
```

上面关于 border-image 的三个属性可以简写为

```css
border-image: linear-gradient(45deg, gold, deeppink) 1;
```

border-radius: 10px 但是实际表现没有圆角。使用 border-image 最大的问题在于，设置的 border-radius 会失效

## 方案二：border-image + overflow: hidden

这个方法也很好理解，既然设置了 background-image 的元素的 border-radius 失效。那么，我们只需要给它加一个父容器，父容器设置 overflow: hidden + border-radius 即可：

```html
<div class="border-image-overflow"></div>
```

```css
.border-image-overflow {
  position: relative;
  width: 200px;
  height: 100px;
  border-radius: 10px;
  overflow: hidden;
}

.border-image-overflow::before {
  content: "";
  position: absolute;
  width: 200px;
  height: 100px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  border: 10px solid;
  border-image: linear-gradient(45deg, gold, deeppink) 1;
}
```

## 方案三：border-image + clip-path

设置了 background-image 的元素的 border-radius 失效。但是在 CSS 中，还有其它方法可以产生带圆角的容器，那就是借助 clip-path。简而言之，这里，我们只需要在 border-image 的基础上，再利用 clip-path 裁剪出一个带圆角的矩形容器即可。

```html
<div class="border-image-clip-path"></div>
```

```css
.border-image-clip-path {
  position: relative;
  width: 200px;
  height: 100px;
  border: 10px solid;
  border-image: linear-gradient(45deg, gold, deeppink) 1;
  clip-path: inset(0 round 10px);
}
```

> clip-path: inset(0 round 10px) 。clip-path: inset() 是矩形裁剪 inset() 的用法有多种，在这里 inset(0 round 10px) 可以理解为，实现一个父容器大小（完全贴合，垂直水平居中于父容器）且 border-radius: 10px 的容器，将这个元素之外的所有东西裁剪掉（即不可见）。

当然，还可以利用 filter: hue-rotate()顺手再加个渐变动画

## 方案四：使用 hue-rotate 实现渐变背景动画

```html
<div></div>
```

```css
div {
  width: 300px;
  height: 180px;
  margin: auto;
  background: linear-gradient(45deg, #ffc107, deeppink, #9c27b0);
  animation: hueRotate 10s infinite alternate;
}

@keyframes hueRotate {
  100% {
    filter: hue-rotate(360deg);
  }
}
```

上面是一个背景流动的动画，所以上面边框可以做如下改进：

```css
.border-image-clip-path {
  width: 200px;
  height: 100px;
  margin: auto;
  border: 10px solid;
  border-image: linear-gradient(45deg, gold, deeppink) 1;
  clip-path: inset(0px round 10px);
  animation: huerotate 6s infinite linear;
  filter: hue-rotate(360deg);
}

@keyframes huerotate {
  0% {
    filter: hue-rotate(0deg);
  }
  100% {
    filter: hue-rorate(360deg);
  }
}
```

可以实现一个背景流动圆角边框效果。

## 方案五：一个标签的实现方案

```html
<div class="border-box"></div>
```

```css
.border-box {
  width: 100px;
  height: 100px;
  border: 4px solid transparent;
  border-radius: 50%;
  background-clip: padding-box, border-box;
  background-origin: padding-box, border-box;
  background-image: linear-gradient(to right, #333, #333), linear-gradient(0deg, rgb(
          174,
          111,
          255
        ) 0%, rgb(46, 179, 255) 100%);
}
```

以上。
