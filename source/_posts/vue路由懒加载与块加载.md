---
title: vue路由懒加载与块加载
date: 2020-11-02
tags: [路由懒加载]
---

之前一直不理解路由的懒加载与块加载到底有什么差别，

后来查阅了相关资料，算是理解了，

特此记录一下

<!-- more -->

## 一、非懒加载

```js
Vue.use(VueRouter);
import VueRouter from "vue-router";
// 这种方式,是页面一打开全部页面都加载完成
import index from "@/components/index";
import login from "@/components/login";
import register from "@/components/register";
const routes = [{ path: "/", component: index }];
const router = new VueRouter({
  routes, // (缩写) 相当于 routes: routes
});
new Vue({
  router,
  render: (h) => h(App),
}).$mount("#app");
```

## 二、懒加载 (按需加载)

```js
const login = () => import("@/components/login");
const register = () => import("@/components/register");
```

减少首屏加载时间,用户跳转页面的时候才加载

## 三、把组件按组分块

有时候我们想把某个路由下的所有组件都打包在同个异步块 (chunk) 中。只需要使用 命名 chunk，一个特殊的注释语法来提供 chunk name (需要 Webpack > 2.4)。
当访问其中一个页面的时候,相同的 webpackChunkName:相同值,的路由会一同加载

```js
const login = () =>
  import(/* webpackChunkName: "userCenter" */ "@/components/login");
const register = () =>
  import(/* webpackChunkName: "userCenter" */ "@/components/register");
```
