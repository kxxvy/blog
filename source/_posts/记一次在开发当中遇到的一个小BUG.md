---
title: 记一次在开发当中遇到的一个小BUG
date: 2019-12-02
tags: [前端,BUG,]
---
今天，在开发中遇到了一个关于谷歌浏览器的一个小BUG

话不多说  直接进入正题

<!-- more -->

正常情况下，是这样的：

<img width=1000px src="https://i.loli.net/2019/12/02/yo4uCGhMYWVdIvF.png" >

我把基本属性往上调了之后，实际情况就变成了这样的

<img width=1000px src="https://i.loli.net/2019/12/02/9f5Mqy8RIiQsHAo.png" >

然而

我放在火狐上调试的时候

一切却是正常的

因此

这个问题只能是出现在谷歌浏览器自身上

后来查到这个是谷歌浏览器存在的一个小BUG

我换顺序失效的原因就是不加前缀的放到后面 根据css优先级是后面覆盖前面

**<font color=orange>总结</font>**：

一般来讲

为了以后避免这种BUG出现

不加前缀的基本属性一律写在后面就好了
