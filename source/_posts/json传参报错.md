---
title: json传参报错
date: 2023-06-13
tags: [json]
---

## 背景

在做小程序开发时，

需要将对象类型的数据传到下一个页面去，

却遇到报错:

> SyntaxError: Unexpected end of JSON input

<!-- more -->

## 解决

> 如果对象的参数或数组的元素中遇到地址，地址中包括?、&这些特殊符号时，对象/数组先要通过 `JSON.stringify` 转化为字符串再通过 `encodeURIComponent` 编码，接收时，先通过 `decodeURIComponent` 解码再通过 `JSON.parse` 转化为 `JSON` 格式的对象/数组

当前页面传参

```js
jumpDetail(data) {
  const info = JSON.stringify(data)
  wx.navigateTo({
    url: `/pages/detail/index?info=${encodeURIComponent(data)}`
  })
}
```

下一个页面接受

```js
onLoad(options) {
  const info = JSON.parse(decodeURIComponent(options.info))
  console.log(info)
}
```

## 总结

在往后的开发中，

传参的时候最好都用 `decodeURIComponent` 先进行编码，

避免后续出现不可预期的问题。
