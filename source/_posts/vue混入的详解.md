---
title: vue混入的详解
date: 2021-07-12
tags: [vue混入]
---

## 简介

混入 (mixins) 是一种分发 Vue 组件中可复用功能的非常灵活的方式。混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被混入该组件本身的选项。

<!-- more -->

## 钩子函数合并

同名钩子函数将混合为一个数组，因此都将被调用。另外，混入对象的钩子函数将在组件自身钩子函数之前调用

```html
<body>
  <div id="app"></div>
</body>
</html>
<script src="./vue.js"></script>
<script>
  var Mixins = {
    created() {
      console.log('Mixins Created')
    }
  }

  new Vue({
    el: '#app',
    mixins: [Mixins],
    created() {
      console.log('#app Created')
    }
  })

  // Mixins Created
  // #app Created
</script>
```

## 数据对象合并

数据对象在内部会进行浅合并 (一层属性深度)，在和组件的数据发生冲突时以组件数据优先（组件的data中变量会覆盖混入对象的data中变量）

```html
<body>
  <div id="app"></div>
</body>
</html>
<script src="./vue.js"></script>
<script>
  var Mixins = {
    data: {
      msg: 'I am Mixins',
      msg1: 'I am Mixins msg1'
    }
    created() {
      console.log('我是组件中的变量：' + this.msg2)
    }
  }

  new Vue({
    mixins: [Mixins],
    el: '#app',
    data: {
      msg: 'I am #app',
      msg2: 'I am msg2'
    }
    created() {
      console.log(this.msg)
      console.log('我是混入对象中的变量：' + this.msg1)
    }
  })

  // 我是组件中的变量：I am msg2'
  // I am #app
  // 我是混入对象中的变量：I am Mixins msg1
</script>
```

## 普通方法合并

当混合值为对象的选项时，例如 methods、components、directive，将被混合为同一个对象，两个对象键名冲突时，取组件对象的键值对

```html
<body>
  <div id="app"></div>
</body>
</html>
<script src="./vue.js"></script>
<script>
  var Mixins = {
    methods: {
      mixin: function() {
        console.log('Mixin')
      },
      mixinTwo: function() {
        console.log('MixinTwo')
      }
    }
  }

  new Vue({
    el: '#app',
    mixins: [Mixins],
    methods: {
      mixin: function() {
        console.log('#app')
      }
    },
    mounted() {
      this.mixin()
      this.mixinTwo()
    }
  })

  // #app
  // MixinTwo
</script>
```

## 局部混入

在 components 目录下创建一个mixins文件夹，并在 mixins 目录下创建一个 mixin.js 文件

![aaa.jpg](https://i.loli.net/2021/09/11/hN3UZ1maVBdbXFP.jpg)

在 mixin.js 文件里写入如下代码

```js
const mixin = {
  data() {
    return {
      msg: '哈哈'
    }
  },
  methods: {
    mixinMethod() {
      console.log(this.msg + '，这是mixin混入的方法')
    }
  }
}

export default mixin
```

在需要的页面引入并使用

```html
<template>
  <div>{{ msg }}</div>
</template>
<script>
import mixin from '../mixins/mixin
export default {
  mixins: [mixin],
  data() {
    return {

    }
  },
  mounted() {
    this.mixinMethod()
  }
}

// 哈哈，这是mixin混入的方法
```

## 全局混入

**1. 在 HTML 中全局混入**

一旦使用全局混入对象，将会影响到所有之后创建的 Vue 实例

```html
<body>
  <div id="app"></div>
</body>
</html>
<script>
  Vue.mixin({
    methods: {
      mixinOne: function() {
        console.log('mixinOne')
      }
    }
  })
  new Vue({
    el: '#app',
    methods: {
      mixinTwo: function() {
        console.log('mixinTwo')
      }
    },
    mounted() {
      this.mixinOne()
      this.mixinTwo()
    }
  })

  // mixinOne
  // mixinTwo
</script>
```

**2. 在 Vue 项目中全局混入**

在 main.js 中写入如下代码

```js
import Vue from 'vue'
import App from './App'
import router from './router'

Vue.config.productionTip = false

Vue.mixin({
  data() {
    return {
      msg: '哈哈'
    }
  },
  methods: {
    mixinMethod() {
      console.log(this.msg + '，这是mixin混入的方法')
    }
  }
})

new Vue({
  el: '#app',
  router,
  components: { App },
  template: '<App/>'
})

```

在组件中直接使用

```html
<template>
  <div>{{ msg }}</div>
</template>
<script>
  export default {
    data() {
      return {

      }
    },
    mounted() {
      this.mixinMethod()
    }
  }

  // 哈哈，这是mixin混入的方法
</script>
```