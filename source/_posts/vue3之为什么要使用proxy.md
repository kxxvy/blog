---
title: vue3之为什么要使用proxy
date: 2021-07-21
tags: [vue3]
---

据说vue3的双向绑定的方式不一样了，

vue3为什么要使用proxy呢，

相比vue2的双向绑定方式又有何差异呢，

让我们深入了解一下。

<!-- more -->

## Object.defineProperty 劫持数据

- 只是对对象的属性进行劫持
- 无法监听新增属性和删除属性
- 需要使用 vue.set, vue.delete
- 深层对象的劫持需要一次性递归
- 劫持数组时需要重写覆盖部分 Array.prototype 原生方法

## Proxy 劫持数据

- 真正的对对象本身进行劫持
- 可以监听到对象新增删除属性
- 只在 getter 时才对对象的下一层进行劫持(优化了性能)
- 能正确监听原生数组方法
- 无法 polyfill 存在浏览器兼容问题

## 总结

Object.defineProperty 是对对象属性的劫持
Proxy 是对整个对象劫持

Object.defineProperty 无法监听新增和删除
Object.defineProperty 无法监听数组部分方法需要重写
Object.defineProperty 性能不好要对深层对象劫持要一次性递归

Proxy 能正确监听数组方法
Proxy 能正确监听对象新增删除属性
Proxy 只在 getter 时才进行对象下一层属性的劫持 性能优化
Proxy 兼容性不好
