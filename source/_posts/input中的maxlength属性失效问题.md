---
title: input中的maxlength属性失效问题
date: 2020-08-23
tags: [input]
---

今天在遇到限制某个输入框的最大长度的需求，比如一个要求输入手机号的输入框：当时我是这样写的

```html
<input type="text" placeholder="请输入手机号码" maxlength="11" />
```

<!-- more -->

如果是上面的这种写法的话，maxlength属性是有效的。

但是type=”text”有一个不好的体验，就是获取焦点后弹出的输入法是默认拼音的那种，不太适合此处要求纯数字的需求。

那么自然会想到改为type=”number”，改为后你会奔溃， 竟然发现maxlength属性不起作用了，可以无限地输入，明明number是限制数字，为什么长度会失效呢，刨根问底。

经过查阅相关资料，最终总结出以下内容：

在html5中的新增的type类型包含：color, date, datetime, datetime-local, month, week, time, email, number, range, search, tel 以及 url。

发现number类型不行后，后面去试了下tel类型，发现maxlength属性又变有效了，不过这样并不是很好。

**<font size=4>最终的解决方法如下：</font>**

```html
<input type="number" placeholder="请输入手机号码" oninput="if(value.length>11)value=value.slice(0,11)" />
```
