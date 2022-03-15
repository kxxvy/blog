---
title: vue组件name的作用
date: 2020-12-12
tags: [vue]
---

在写 vue 项目的时候会遇到给组件命名,

这里的 name 非必选项，看起来好像没啥用处，

平时也没怎么用到过，

但是实际上用处还是挺多的，

特此记录一下

<!-- more -->

## 一、当使用 keep-alive 对组件进行过滤时

举个例子：

我们有个组件命名为 detail,其中 dom 加载完毕后我们在钩子函数 mounted 中进行数据加载

```js
export default {
  name:'Detail'
}，
mounted(){
  this.getInfo();
}，
methods:{
  getInfo(){
     axios.get('/xx/detail.json',{
       params:{
        id:this.$route.params.id
       }
     }).then(this.getInfoSucc)
   }
 }
```

因为我们在 App.vue 中使用了 keep-alive 导致我们第二次进入的时候页面不会重新请求，即触发 mounted 函数。

有两个解决方案,一个增加 activated()函数,每次进入新页面的时候再获取一次数据。

还有个方案就是在 keep-alive 中增加一个过滤，如下图所示：

```js
<div id="app">
  <keep-alive exclude="Detail">
    <router-view />
  </keep-alive>
</div>
```

## 二、DOM 做递归组件时

比如说 detail.vue 组件里有个 list.vue 子组件，递归迭代时需要调用自身 name

list.vue:

```js
<div>
   <div v-for="(item,index) of list" :key="index">
     <div>
       <span class="item-title-icon"></span>
       {{item.title}}
     </div>
     <div v-if="item.children" >
       <detail-list :list="item.children"></detail-list>
     </div>
   </div>
</div>
<script>
export default {
 name:'DetailList',//递归组件是指组件自身调用自身
 props:{
   list:Array
 }
}
</script>
```

list 数据：

```js
const list = [
  {
    title: "A",
    children: [
      {
        title: "A-A",
        children: [
          {
            title: "A-A-A",
          },
        ],
      },
      {
        title: "A-B",
      },
    ],
  },
  {
    title: "B",
  },
  {
    title: "C",
  },
  {
    title: "D",
  },
];
```

## 三、当你用 vue-tools 时

vue-devtools 调试工具里显示的组见名称是由 vue 中组件 name 决定的

以上。
