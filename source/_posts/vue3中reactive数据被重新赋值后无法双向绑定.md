---
title: vue3中reactive数据被重新赋值后无法双向绑定
date: 2021-12-26
tags: [vue3]
---

这是应为 reactive 数据被重新赋值后，原来数据的代理函数和最新的代理函数不是同一个，无法被触发

<!-- more -->

```js
const list = reactive({});
```

当你接收到接口数据，不要直接赋值 比如 list = data

这样会丢失响应式

可以这样做

## 方式一

```js
const list = reactive({
  arr: [],
});
list.arr = data.arr;
```

## 方式二

将 list 声明为 ref 方式

```js
const list = ref([]);
list.value = data;
```

## 推荐写法

```js
import { reactive, toRefs } from 'vue

setup(props, context) {
  const state = reactive({
    message: ref(''),
    count: ref(0),
    checked: ref(false),
    tableData: []
  })
  // 赋值
  state.tableData = data

  return {
    ...toRefs(state)
  }
}
```
