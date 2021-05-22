---
title: VUE中11种组件传值方法总结
date: 2020-12-13
tags: [vue, 组件传值]
---

![20201216164359342.png](https://i.loli.net/2021/05/22/eG1zXA5Uu7qZ9Cx.png)

<!-- more -->

## 1. 父组件给子组件传值

子组件

```html
// children
<template>
    <section>
        父组件传过来的消息是：{{myMsg}}
    </section>
</template>

<script>
export default {
  name: "Children",
  components: {},
  props:{
    msg: {
      type: String,
      default: 'HELLO WORLD'
    }
  },
  data() {
    return {
      myMsg:this.msg
    }
  }
}
</script>
```

父组件

```html
<template>
  <div class="parent">
    <Children :msg="message"></Children>
  </div>
</template>

<script>
import Children from '../components/Children'

export default {
  name: 'Parent',
  components: {
    Children
  },
  data() {
    return {
      message:'hello world'
    }
  },
}
</script>
```

## 2.子组件给父组件传值

父组件

```html
// parent
<template>
  <div id="app">
    <children @getChildrenVal="getChildrenVal"></children>
  </div>
</template>

<script>
import Children from '../src/components/children.vue'

export default {
  name: 'App',
  components: {
    Children
  },
  methods: {
    getChildrenVal(value) {
      console.log(value)
    }
  }
}
</script>
```

子组件

```html
// children
<template>
  <div>
    <button @click="toParent()"></button>
  </div>
</template>

<script>
export default {
  name: 'children',
  data() {
    return {
      msg: 1
    }
  },
  methods: {
    toParent() {
      this.msg++
      this.$emit('getChildrenVal', this.msg)
    }
  }
}
</script>
```

## 3.兄弟组件中使用EventBus传参

父组件

```html
// parent
<template>
  <div id="app">
    <children-one></children-one>
    <children-two></children-two>
  </div>
</template>

<script>
import ChildrenOne from '../src/components/children.vue'
import ChildrenTwo from './components/childrenTwo.vue'

export default {
  name: 'App',
  components: {
    ChildrenOne,
    ChildrenTwo
  }
}
</script>
```

EventBus.js

```javascript
// EventBus.js
import Vue from 'vue'
export default new Vue()
```

子组件一

```html
// childrenOne
<template>
  <div>
    <button @click="pushMsg">组件一传参给组件二</button>
  </div>
</template>

<script>
import eventBus from '../utils/eventBus.js'

export default {
  name: 'childrenOne',
  data() {
    return {
      msg: 1
    }
  },
  methods: {
    pushMsg() {
      this.msg++
      eventBus.$emit('pushMsg', this.msg)
    }
  }
}
</script>
```

子组件二

```html
// 组件二
<template>
  <div>
    组件二{{msg}}
  </div>
</template>

<script>
import eventBus from '../utils/eventBus.js'

export default {
  name: 'children',
  data() {
    return {
      msg: 1
    }
  },
  mounted() {
    eventBus.$on('pushMsg', (value) => {
      this.msg = value
    })
  }
}
</script>
```

## 4. 路由间传值

（1） 使用？传值

```javascript
// a页面(?后传参)
this.$router.push('/B?name=demo');

//b页面（query接收url上面的参数）
var name = this.$route.query.name
```

（2） 使用：传值

```javascript
// 路由配置
{
  path: '/B/:name',
  name: B,
  component: () => import('../views/B.vue')
}
// 通过this.$route.params.name获取
```

（3） 父子传值

```html
// parent页面
<router-view :name=name></router-view>
```

```javascript
// children页面
// 由于name更新之后并没有刷新路由，所以在mounted中无法监听到name的变化，得使用watch监听
props: ['name'],
watch: {
  name(oldVal,newVal) {
    console.log(newVal)
  }
}
```

## 5. 通过refs传参

parent页面（通过ref调用子组件的方法并传入参数）

```javascript
<template>
  <div id="app">
    <button @click="setMsg">改变children的msg值</button>
    <children-one ref="childrenOne"></children-one>
  </div>
</template>

<script>
import ChildrenOne from '../src/components/children.vue'

export default {
  name: 'App',
  components: {
    ChildrenOne,
  },
  methods: {
    setMsg() {
      this.$refs.childrenOne.setMsg('msg值已经被改变')
    }
  }
}
</script>
```

children页面（通过setMsg函数获取传入的参数值）

```javascript
<template>
  <div>
    {{ msg }}
  </div>
</template>

<script>
export default {
  name: 'childrenOne',
  data() {
    return {
      msg: 1
    }
  },
  methods: {
    setMsg(value) {
      this.msg = value
    }
  }
}
</script>
```

## 6. 通过依赖注入provide和inject给后代

父组件

```javascript
<template>
  <div id="app">
    <children-one></children-one>
  </div>
</template>

<script>
import ChildrenOne from '../src/components/children.vue'

export default {
  name: 'App',
  components: {
    ChildrenOne,
  },
  provide() {
    return {
      name: 'demo'
    }
  }
}
</script>
```

子组件

```javascript
<template>
  <div>
    {{ name }}
  </div>
</template>

<script>
export default {
  name: 'childrenOne',
  inject: ['name']
}
</script>
```

## 7.祖传孙使用$attrs

```javascript
// GrandParent
<template>
  <div id="app">
    <children-one name="xp" age="18" value="123"></children-one>
  </div>
</template>

<script>
import ChildrenOne from '../src/components/children.vue'

export default {
  name: 'App',
  components: {
    ChildrenOne,
  }
}
</script>
```

```javascript
// parent
<template>
  <div>
    组件一：{{name}}
    <children-two v-bind="$attrs"></children-two>
  </div>
</template>

<script>
import ChildrenTwo from './childrenTwo.vue'

export default {
  name: 'childrenOne',
  // 如果父组件接收了祖父传过来的name参数，那么这个参数就不会传给子组件，子组件就获取不到这个值
  props: ['name'],
  components: {
    ChildrenTwo
  }
}
</script>
```

```javascript
// children
<template>
  <div>
    组件二{{name}} {{age}} {{value}}
  </div>
</template>

<script>
export default {
  name: 'children',
  props: ['name','age','value'],
  data() {
    return {
    }
  },
  mounted() {
  }
}
</script>
```

## 8. 孙传祖使用$listeners

GrandParent.vue

```javascript
// GrandParent
<template>
  <div id="app">
    <children-one @eventOne="eventOne"></children-one>
    {{ msg }}
  </div>
</template>

<script>
import ChildrenOne from '../src/components/children.vue'

export default {
  name: 'App',
  components: {
    ChildrenOne,
  },
  data() {
    return {
      msg: ''
    }
  },
  methods: {
    eventOne(value) {
      this.msg = value
    }
  }
}
</script>
```

parent.vue

```javascript
// parent
<template>
  <div>
    <children-two v-on="$listeners"></children-two>
  </div>
</template>

<script>
import ChildrenTwo from './childrenTwo.vue'

export default {
  name: 'childrenOne',
  components: {
    ChildrenTwo
  }
}
</script>
```

children.vue

```javascript
// children
<template>
  <div>
    <button @click="setMsg">点击传给祖父</button>
  </div>
</template>

<script>
export default {
  name: 'children',
  methods: {
    setMsg() {
      this.$emit('eventOne', '123')
    }
  }
}
</script>
```

## 9. $parent

通过$parent获取父组件的实例，拿到父组件data中的参数

父组件

```javascript
<template>
  <div>
    <children-two></children-two>
  </div>
</template>

<script>
import ChildrenTwo from './childrenTwo.vue'

export default {
  name: 'childrenOne',
  components: {
    ChildrenTwo
  },
  data() {
    return {
      msg: 'msg'
    }
  }
}
</script>
```

子组件

```javascript
<template>
  <div>
    <button @click="getMsg">点击获取父亲传来的参数值</button>
    <p>父组件传来的参数值为：{{ msg }}</p>
  </div>
</template>

<script>
export default {
  name: 'children',
  data() {
    return {
      msg: ''
    }
  },
  methods: {
    getMsg() {
      console.log(this.$parent)
      this.msg = this.$parent.msg
    }
  }
}
</script>
```

## 10. sessionStorage

sessionStorage是浏览器中的全局对象，它会在当前页面关闭时被清除,所以我们可以在一个页面中共享一份数据。

sessionStorage操作方法

```javascript
// 保存数据到 sessionStorage
sessionStorage.setItem('key', 'value');

// 从 sessionStorage 获取数据
sessionStorage.getItem('key');

// 从 sessionStorage 删除保存的数据
sessionStorage.removeItem('key');

// 从 sessionStorage 删除所有保存的数据
sessionStorage.clear();
```

由于sessionStorage中只能存储字符串类型，如果想存储对象的话需使用JSON.stringify(obj)去转换为json字符串，然后存储到sessionStorage中。

这里给推荐使用插件good-storage，可直接调用api，将想缓存的数据直接存进去即可。

```javascript
import storage from 'good-storage'

// localStorage
storage.set(key,val) 

storage.get(key, def)

// sessionStorage
storage.session.set(key, val)

storage.session.get(key, val)
```

## 11. Vuex

通过mutation进行state的同步修改，action进行state的异步修改。
在vue组件中使用$state拿取参数，这里就不做过多的描述（如果不是大型项目中建议不使用）。
