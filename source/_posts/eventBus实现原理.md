---
title: eventBus实现原理
date: 2023-10-27
tags: [eventBus, 发布订阅模式]
---

## 前言

eventBus 是组件消息传递的一种方式，基于一个消息中心，订阅和发布消息的模式，称为发布订阅模式

<!-- more -->

## 消息中心

Event.js 代码封装

```js
class Event {
  constructor() {
    this.callbacks = {};
  }

  // 解除监听
  $off(name) {
    this.callbacks[name] = null;
  }

  // 提交通信封装
  $emit(name, ...args) {
    let cbs = this.callbacks[name];
    if (cbs) {
      cbs.forEach((c) => {
        c.call(this, ...args);
      });
    }
  }

  // 监听封装
  $on(name, fn) {
    (this.callbacks[name] || (this.callbacks[name] = [])).push(fn);
  }
}
export default new Event();
```

## 订阅消息

需要监听通信的组件

```js
import Bus from "../../utils/Event";
export default {
  created() {
    Bus.$on("setMessage", (obj) => {
      console.log(obj);
    });
  },
};
```

## 发布消息

发起通信的组件

```js
import Bus from "../../utils/Event";
export default {
  created() {
    Bus.$emit("setMessage", {
      name: "kv",
      age: 18,
    });
  },
};
```
