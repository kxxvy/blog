---
title: 如何在HTML中限制input输入框只能输入纯数字
date: 2021-05-22
tags: [input]
---

## 限制 input 输入框只能输入纯数字

<font color=red>1、onkeyup = "value=value.replace(/[^\d]/g,'')"</font>

> 使用 <font color=red>`onkeyup`</font> 事件，有 <font color=red>`bug`</font> ，那就是在中文输入法状态下，输入汉字之后直接回车，会直接输入字母


<!-- more -->

<font color=red>2、onchange = "value=value.replace(/[^\d]/g,'')"</font>

> 使用 <font color=red>`onchange`</font> 事件，在输入内容后，只有 <font color=red>`input`</font> 丧失焦点时才会得到结果，并不能在输入时就做出响应

<font color=red>3、oninput = "value=value.replace(/[^\d]/g,'')"</font>

> 使用 <font color=red>`oninput`</font>事件，完美的解决了以上两种问题，测试暂时还没有出现其它问题。


**代码示例**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>input</title>
</head>
<body>
  只能输入纯数字的输入框：<input type="text" name="" oninput="value=value.replace(/[^\d]/g,'')">
</body>
</html>
```