---
title: 如何在Redux中更新状态后触发回调？
date: 2021-12-05
tags: [Redux]
---

在 React 中，状态不会立即更新，因此我们可以在 setState（state，callback）中使用回调。但是如何在 Redux 中做到这一点？

调用 this.props.dispatch（updateState（key，value）），我需要立即对更新后的状态做一些事情。

有没有什么方法可以调用最新状态的回调？

<!-- more -->

## 解决方案

**1、 componentDidUpdate 检查值是否已更改，然后执行某些操作...**

```js
componentDidUpdate(prevProps){
 if (prevProps.value！== this.props.value) {alert（prevProps.value)}
}
```

**2、 redux-promise （中间件将调度承诺的已解析值）**

```js
export const updateState = (key，value )=>
Promise.resolve({
  type：'UPDATE_STATE'，
  key: value
})

```

然后在组件中

```js
this.props.dispatch(updateState(key，value) ).then(()=> {
 alert(this.props.value)
}）

```

**3、redux-thunk**

```js
export const updateState = (key，value) => dispatch => {
dispatch({
  type：'UPDATE_STATE'，
  key, value
})
 return Promise.resolve（）
}

```

然后在组件中

```js
this.props.dispatch(updateState(key，value)).then(() => {
  alert（this.props.value）
})

```
