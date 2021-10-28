---
title: 谈一谈对CSS盒模型及BFC的认识
date: 2019-09-05
tags: [前端, 面试题, 盒模型]
---

专业的面试，一定会问 CSS 盒模型。对于这个题目，我们要回答一下几个方面:

(1)基本概念：content、padding、margin。
(2)标准盒模型、IE 盒模型的区别。
(3)CSS 如何设置者两种模型(即：如何设置某个盒子为其中一个模型)？
(4)JS 如何设置、获取盒模型对应的宽和高？
(5)实例题：根据盒模型解释边距重叠。
(6)BFC(边距重叠解决方案)或 IFC。

<!-- more -->

## 标准盒模型和 IE 盒模型

标准盒子模型：
![687474703a2f2f696d672e736d79687661652e636f6d2f323031352d31302d30332d6373732d32372e6a7067.jpg](https://i.loli.net/2019/09/05/l3yjesugft4UFOZ.jpg)

IE 盒子模型：
![687474703a2f2f696d672e736d79687661652e636f6d2f323031352d31302d30332d6373732d33302e6a7067.jpg](https://i.loli.net/2019/09/05/lHgqkQZn1UIiYa5.jpg)

上图显示：

在 CSS 盒子模型(Box Model)规定了元素处理元素的几种方式：

- width 和 height：<font size=3 color=red>内容</font>的宽度、高度(不是盒子的宽度、高度)。
- padding：内边距。
- border：边框。
- margin：外边距。

CSS 盒模型和 IE 盒模型的区别：

- 在<font size=3 color=red>标准盒子模型</font>中，width 和 height 指的是<font size=3 color=red>内容区域</font>的宽度和高度。增加内边距、边框和外边距不会影响内容区域的尺寸，但是会增加元素框的总尺寸。
- <font size=3 color=red>IE 盒子模型</font>中，width 和 height 指的是<font size=3 color=red>内容区域</font>+<font size=3 color=red>border</font>+<font size=3 color=red>padding</font>的宽度和高度。

## CSS 如何设置这两种模型

代码如下：

```css
/* 设置当前盒子为 标准盒模型(默认) */
box-sizing: content-box;

/* 设置当前盒子为 IE盒模型 */
box-sizing: border-box;
```

备注：盒子默认为标准盒模型。

<font size=5>JS 如何设置、获取盒模型对应的宽和高</font>

### 方式一：通过 DOM 节点的 style 样式获取

```javascript
element.style.width / height;
```

缺点：通过这种方式，<font color="red">只能获取行内样式</font>，不能获取`内嵌`和`外链`的样式。
这种方式有局限性，但应该了解。

### 方式二(通用型)

```javascript
window.getComputed(element).width / height;
```

方式二能兼容 Chrome、火狐。是<font color="green">通用型</font>方式。

### 方式三(IE 独有的)

```javascript
element.currentStyle.width / height;
```

和方式二相同，但这种方式止只有<font color="red">IE 独有</font>。获取到的即时运行完之后的宽高(三种 css 样式都可以获取)。

### 方式四

```javascript
element.getBoundingClientRect().width / height;
```

此 api 的作用是：获取一个元素的绝对位置。绝对位置是视窗 viewport 左上角的绝对位置。
此 api 可以拿到四个属性：<font color="orange">left</font>、<font color="orange">top</font>、<font color="orange">width</font>、<font color="orange">height</font>。

## 总结

上面的四种方式，要求能说出来区别，以及哪个的通用性更强。

## margin 塌陷/margin 重叠

标准文档流中，竖直方向的 margin 不叠加，<font color="red">只取较大的值</font>作为 margin(水平方向的 margin 是可以叠加的，即水平没有塌陷现象)。
PS：<font color="red">如果不在标准流</font>，比如盒子都浮动了，那么两个盒子之间是没有 margin 重叠的现象的。

<font size=5>BFC(边距重叠解决方案)</font>

### BFC 的概念

BFC(Block Formatting Context)：<font color="orange">块级格式化上下文</font>。你可以把它理解成一个独立的区域。
另外还有个概念 IFC。不过，BFC 问得更多。

### BFC 的原理/BFC 的布局规则【非常重要】

BFC 的原理，其实也就是 BFC 的渲染规则(能说出一下四点就够了)。包括：

- (1)BFC 内部的子元素，在垂直方向，边距会发生重叠。
- (2)BFC 在页面中是独立的容器，外面的元素不会影响里面的元素，反之亦然。
- (3)BFC 区域不与旁边的`float box`区域重叠。(可以用来清除浮动带来的影响)。
- (4)计算 BFC 的高度时，浮动的子元素也参与计算。

### 如何生成 BFC

有以下几种方法：

- 方法 1：overflow：不为 visible，可以让属性是 hidden、auto。【最常用】
- 方法 2：浮动中：float 的属性值不为 none。意思是，只要设置了浮动，当前元素就创建了 BFC。
- 方法 3：定位中：只要 position 的值不是 static 或者是 relative 即可，可以是 absolute 或 fixed，也就生成了一个 BFC。
- 方法 4：display 为 inline-block，table-caption，flex，inline-flex

参考链接：

- [BFC 原理详解](https://segmentfault.com/a/1190000006740129)
- [BFC 详解](https://www.jianshu.com/p/bf927bc1bed4)
- [前端精选文摘：BFC 神奇背后的原理](https://www.cnblogs.com/lhb25/p/inside-block-formatting-ontext.html)
