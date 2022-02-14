---
title: CSS实现div填满剩余高度的几种方式
date: 2020-08-24
tags: [css]
---

## 一、需求

- `nav`和`content`盒子的高度和浏览器高度相同，且不能出现纵向滚动条
- 绿色盒子高度固定，比如: 50px
- 紫色盒子填充剩余的高度

```html
<div id="main">
  <div id="nav">nav</div>
  <div id="content">content</div>
</div>
```

<!-- more -->

## 二、实现方式

### 方式一: 绝对定位

```css
<style>
  html, body {
    height: 100%;
    margin: 0;
    padding: 0;
  }
  #main {
    width: 100%;
    background-color: #999;
    height: 100%;
  }
  #nav {
    background-color: #85d989;
    height: 50px;
  }
  #content {
    background-color: #cc85d9;
    width: 100%;
    position: absolute;
    top: 50px;
    left: 0;
    bottom: 0;
  }
</style>
```

### 方式二: box-sizing 的两种方法

**1、方法一**

```css
<style>
  html, body {
    height: 100%;
    margin: 0;
    padding: 0;
  }
  #main {
    padding: 50px 0 0;
    background-color: #999;
    height: 100%;
    box-sizing: border-box;
  }
  #nav{
    background-color: #85d989;
    height: 50px;
    /* 方法一 */
    marin: -50px 0 0;
    width: 100%;
  }
  #content {
    background-color: #cc85d9;
    height: 100%;
  }
</style>
```

**2、方法二**

```css
<style>
  html, body {
    height: 100%;
    margin: 0;
    padding: 0;
  }
  #main {
    padding: 50px 0 0;
    background-color: #999;
    height: 100%;
    box-sizing: border-box;
  }
  #nav{
    background-color: #85d989;
    height: 50px;
    /* 方法二 */
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
  }
  #content {
    background-color: #cc85d9;
    height: 100%;
  }
</style>
```

### 方式三: flex 布局

```css
<style>
  html, body {
    height: 100%;
    margin: 0;
    padding: 0;
  }
  #main {
    display: flex;
    flex-direction: column;
    padding: 50px 0 0;
    background-color: #999;
    height: 100%;
  }
  #nav {
    background-color: #85d989;
    height: 50px;
    width: 100%;
  }
  #content {
    flex: 1;
    background-color: #cc85d9;
    width: 100%;
  }
</style>
```
