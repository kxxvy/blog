---
title: 关于eventBus的学习
date: 2023-10-27
tags: [eventBus, 发布订阅模式]
---

## 一、前言

`eventBus` 是组件消息传递的一种方式，基于一个消息中心，订阅和发布消息的模式，称为发布订阅模式

`eventBus`在`vue`中经常用来解决跨组件消息传递的问题，但对它的使用要特别注意，否则会产生很严重的后果

<!-- more -->

## 二、原理

1、消息中心

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

2、订阅消息

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

3、发布消息

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

## 三、副作用

> 如果不及时取消订阅，则回调函数仍会执行，更严重的是，如果在事件处理回调函数中引用了外部变量形成了闭包，则会导致内存泄漏。

```js
<div id="app">
  <h2>不及时取消订阅的问题</h2>
  <button @click="showCom1=!showCom1">
    {{ showCom1 ? "销毁" : "重建" }}组件1
  </button>
  <com1 v-if="showCom1"></com1>
</div>
<script>
  Vue.prototype.$eventBus = new Vue()
  Vue.component('com1', {
    template: `<div>com1</div>`
    created() {
      console.log("创建com1")
      this.$eventBus.$on('event1', function f1(d) {
        console.log(d, 'com1 listen... event1')
      })
    }
  })
  Vue.component('com2', {
    template: `<div>com2</div>`
    created() {
      console.log("创建com2")
      this.$eventBus.$on('event2', function f2(d) {
        console.log(d, 'com2 listen... event2')
      })
    }
  })
  var vm = new Vue({
    el: "#app",
    data: {
      showCom1: true
    },
    created() {
      setInterval(() => {
        const d = Date.now()
        this.$eventBus.$emit('event1', d)
        this.$eventBus.$emit('event2', d)
      }, 3000)
    }
  })
</script>
```

**Q: `com1` 组件被销毁后，它在 `created` 中订阅的 `event1` 事件还能再收到吗？对应的回调函数还能再执行吗？**

**A：还能收到。原因很简单：事件订阅这功能是`$eventBus` 对象完成的，与这个 `com1` 组件无关。**

上面的代码执行的效果，是这样的：

- 销毁组件 1 之后，它还能正常收到 `event1` 事件，并执行回调；
- 再次创建组件 1 后，它会再订阅 `event1` 事件，所以结果是执行两次回调。

如果把 `com1` 组件内容改成如下：

```js
Vue.component('com1', {
  template: `<div>com1</div>`
  created() {
    console.log("创建com1")
    let m = 1 * 1024 * 1024
    let arr = new Array(m).fill('a')
    this.$eventBus.$on('event1', function f1(d) {
      // 注意：这里有一个闭包
      console.log(d, 'com1 listen... event1', arr[1])
    })
  }
})
```

在回调函数 `f1` 中引用函数之外的变量 `arr`，这里有一个闭包。

在浏览器的调试工具中的 `memory` 添加一个快照，在点击页面上的"销毁组件 1"后，再次添加一个快照，通过对比两次快照的内存可以发现这个空间并没有释放掉。

**解决方案：**

```js
destroyed() {
  this.$eventBus.$off("event1")
}
```

调试结果如下：

可见，`com1` 删除之后，这个数值的空间释放掉了，同时它的事件监听函数也不会再执行了。

## 总结

综上，开发时要养成一个良好的习惯，使用 `eventBus` 或发布订阅模式时要及时取消订阅相应的事件。
