---
title: setState是同步还是异步问题
date: 2021-11-24
tags: [react]
---


## 一、前言

在写react时，

我们会发现setState有时候表现为同步，

有时候却表现为异步，

现在我们一起来探索一下。

<!-- more -->

## 二、验证

**验证一：在setTimeout中的更新：**

```js
changeText() {
  setTimeout(() => {
    this.setState({
      message: "Hello world"
    })
    console.log(this.state.message); // Hello world
  }, 0)
}
```

> 此时setState表现为同步。

**验证二：原生DOM事件：**

```js
componentDidMount() {
  const btnEl = document.getElementById("btn")
  btnEl.addEventListener("click", () => {
    this.setState({
      message: "Hello world"
    })
    console.log(this.state.message)  // ""
  })
}
```

> 此时setState表现为异步。

## 三、总结

我们可以发现setState在不同的情况下表现是不同的，其可分为两种情况：

> 在组件生命周期或react合成事件中，setState是异步；
> 在setTimeout或者原生dom事件中，setState是同步。
