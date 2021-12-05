---
title: Vue3 Composition API学习
date: 2021-08-12
tags: [vue3]
---

## 前言

vue3 最大的改变是加入了这个灵感来源于 React Hook 的 Composition API（组成 API），

让我们来学习一下这和 vue2 的 Options api 有何差异

<!-- more -->

## 一、小试牛刀

先看一下基础例程：

```js
<template>
  <button @click="increment">
    Count is: {{ state.count }}, double is: {{ state.double }}
  </button>
</template>

<script>
  import { reactive, computed } from "vue";

  export default {
    setup(props, context) {
      const state = reactive({
        count: 0,
        double: computed(() => state.count * 2),
      });

      function increment() {
        state.count++;
      }

      return {
        state,
        increment,
      };
    },
  };
</script>
```

从上面的基础例程可以看到，vue3 的.vue 组件大体还是和 vue2 一致，由 template、script 和 style 组成，作出的改变有以下几点：

- 组件增加了 setup 选项，组件内所有的逻辑都在这个方法内组织，返回的变量或方法都可以在模板中使用。
- vue2 中 data、computed 等选项仍然支持，但使用 setup 时不建议再使用 vue2 中的 data 灯选项。
- 提供了 reactive、computed、watch、onMounted 等抽离的接口代替 vue2 中 data 等选项。

## 二、为什么使用 Composition API？

为什么使用 Composition API 代替 Options API？难道将各种类型的代码分类写在对应的地方不比把所有的代码都写在一个 setup 函数中要好？要解答这两个问题，我们首先来思考一下 Options API 的局限。

**代码组织**

Options API 是把代码分类写在几个 Option 中：

```js
export default {
  components: {

  },
  data() {

  },
  computed: {

  },
  watch: {

  },
  mounted() {

  },
```

当组件比较简单只有一个逻辑的时候，稍微麻烦的是用户必须在几个 Option 之间跳来跳去，这个是小问题，写代码哪能不用鼠标呢 😄？挑战往往在复杂的情况下才会出现。当一个 vue 组件内存在多个逻辑时会怎样呢？

- 使用 Options API 时，相同的逻辑写在不同的地方，各个逻辑的代码交叉错乱，这对维护别人代码的开发者来说绝不是一件简单的事，理清楚这些代码都需要花费不少时间。

- 而使用 Composition API 时，相同的逻辑可以写在同一个地方，这些逻辑甚至可以使用函数抽离出来，各个逻辑之间界限分明，即便维护别人的代码也不会在“读代码”上花费太多时间（前提是你的前任会写代码）。
  必须指出的是，Composition API 提高了代码的上限，也降低了代码的下限。在使用 Options API 时，即便再菜的鸟也能保证各种代码按其种类进行划分。但使用 Composition API 时，由于其开放性，出现什么代码是无法想象的。但毫无疑问，Options API 到 Composition API 是 vue 的一个巨大进步，vue 从此可以从容面对大型项目。

  **逻辑抽取与复用**

在 vue2 中要实现逻辑复用主要有两种方式：

1、mixin

mixin 的确能抽取逻辑并实现逻辑复用（我更多用它来定义接口），但这种方式着实不好，mixin 更多是一种代码复用的手段： 1.命名冲突。mixin 的所有 option 如果与组件或其它 mixin 相同会被覆盖（这个问题可以使用 Mixin Factory 解决）。 2.没有运行时实例。顾名思义，mixin 不过是把对应的 option 混入组件内，运行时不存在所抽取的逻辑实例。 3.松散的关系。Options API 注定所抽取的逻辑的组织是松散，逻辑内部之间的关系也是松散的。 4.含蓄的属性增加。mixin 加入的 option 是含蓄的，新手会迷惑于莫名其妙就存在的一个属性，尤其是在有多个 mixin 的时候，无法知道当前属性是哪个 mixin 的。
2、Scoped slot

scoped slot 也可以实现逻辑抽取，使用一个组件抽取逻辑，然后通过作用域插槽暴露给子组件。

```js
<template>
  <div>
    <!-- 暴露逻辑的数据给子组件 -->
    <slot :data="data"></slot>
  </div>
</template>
<script>
export default {
  // 在这里完成逻辑
  data() {

  }
}
</script>
```

使用的时候

```js
<template>
<!--传入未排序的数据unSortdata-->
<GenericSort data="unSortdata">
   <template v-slot="sortData">
   <!-- 使用经过处理后的sortData数据 -->

   <template>
</GenericSearch>
</template>
```

但这种方式还是有许多缺点：

- 增加缩进。一两个时没多大影响，但过多时使可读性变差。 -一般需要增加配置，不灵活。需要在 slot 上增加配置，以应对更多的情况。 -性能差。仅为了抽取逻辑，需要创建维护一个组件实例。
  3、Composition Function

在 vue3 中提供了一种叫 Composition Function 的方式，这种方式允许像函数般抽离逻辑：

```js
<template>
  <div>
   <p> {{count}}</p>
    <button @click="onClick" :disabled="state">Start</button>
  </div>
</template>

<script>
  import {ref} from 'vue'
  // 倒计时逻辑的Composition Function
  const useCountdown = (initialCount) => {
    const count = ref(initialCount)
    const state = ref(false)
    const start = (initCount) => {
      state.value = true;
      if (initCount > 0) {
        count.value = initCount
      }
      if (!count.value) {
        count.value = initialCount
      }
      const interval = setInterval(() => {
        if (count.value === 0) {
          clearInterval(interval)
          state.value = false
        } else {
          count.value--
        }
      }, 1000)
    }
    return {
      count,
      start,
      state
    }
  }

  export default {
   setup() {
    // 直接使用倒计时逻辑
     const {count, start, state} = useCountdown(10)
     const onClick = () => {
        start()
     }
     return  {count, onClick, state}
   }
  }
</script>
```

vue3 建议使用如 React hook 中一样使用 use 开头命名抽取的逻辑函数，如上代码抽取的逻辑几乎如函数一般，使用的时候也极其方便，完胜 vue2 中抽取逻辑的方法。

**ts 类型支持**
  vue2 比较令人诟病的地方还是对 ts 的支持，对 ts 支持不好是 vue2 不适合大型项目的一个重要原因。其根本原因是 Vue 依赖单个 this 上下文来公开属性，并且 vue 中的 this 比在普通的 javascript 更具魔力（如 methods 对象下的单个 method 中的 this 并不指向 methods，而是指向 vue 实例）。换句话说，尤大大在设计 Option API 时并没有考虑对 ts 引用的支持。那 vue2 中是怎样做到对 ts 的支持？

```js
<script lang="ts">
    import { Vue, Component, Prop } from 'vue-property-decorator'

    @Component
    export default class YourComponent extends Vue {
      @Prop(Number) readonly propA: number | undefined
      @Prop({ default: 'default value' }) readonly propB!: string
      @Prop([String, Boolean]) readonly propC: string | boolean | undefined
    }
</script>
```

vue2 对 ts 的支持主要是通过 vue class component，还需引入 vue-property-decorator 包，该库完全依赖于 vue-class-component 包。咋一看，这不是支持了吗？下面聊聊这种方式的缺点：

- vue class component 与 js 的 vue 组件差异太大，另外需要引入额外的库，学习成本大幅度增高。
- 依赖于装饰器语法。而目前装饰器目前还处于 stage2 阶段(可查看 tc39 decorators)，在实现细节上还存在许多不确定性，这使其成为一个相当危险的基础。
- 复杂性增高。采用 Vue Class Component 且需要使用额外的库，相比于简单的 js vue 组件，显然复杂化。
  这些原因让人望而却步，vue2 的 ts 项目数量不多也是可以让人理解的。相比与 vue2，vue3 对 ts 的支持则好得多：

- vue3 中是在 setup 中进行编程，setup 不依赖 this,大部分 API 大多使用普通的变量和函数，它们天然类型友好。
- 用 Composition API 编写的代码可以享受完整的类型推断，几乎不需要手动类型提示。这也意味着用提议的 API 编写的代码在 TypeScript 和普通 JavaScript 中看起来几乎相同。
- 这些接口已获得更好的 IDE 支持，即使非 TypeScript 用户也可以从键入中受益。

## 三、接口一览

### 1.setup

setup 是 vue 新增的一个选项，它是组件内使用 Composition API 的入口。setup 中是没有 this 上下文的（js 函数都有 this，但是 setup 的 this 跟 vue2 的 this 不是一个东西，已经完全没用了，故为没有）。

- 执行时机

setup 是在创建 vue 组件实例并完成 props 的初始化之后执行，也是在 beforeCreate 钩子之前执行,这意味着 setup 中无法使用其它 option（如 data）中的变量，而其它 option 可以使用 setup 中返回的变量。

- 与模板结合使用

如果 setup 返回一个对象，这个对象的所有属性会合并到 template 的渲染上下文中，也就是说可以在 template 中使用 setup 返回的对象的属性。

```js
<template>
  <div>{{ count }} {{ object.foo }}</div>
</template>

<script>
import { ref, reactive } from 'vue'

export default {
  setup() {
    const count = ref(0)
    const object = reactive({ foo: 'bar' })

    // 暴露至模板中
    return {
      count,
      object
    }
  }
}
</script>
```

- 与 Render Function/jsx 结合使用

setup 也可以返回 render function，这个时候则与 vue2 中的 render 方法 l 类似，此时不需要 template。

```js
import { h, ref, reactive } from "vue";

export default {
  setup() {
    const count = ref(0);
    const object = reactive({ foo: "bar" });

    return () => h("div", [count.value, object.foo]);
  },
};
```

- setup 的参数

setup 有两个参数，第一个参数为 props，是组件的所有参数，与 vue2 中一样，参数是响应式的，改变会触发更新；第二个参数是 setup context，包含 emit 等属性。下面是其函数签名：

```js
interface Data {
  [key: string]: unknown
}

interface SetupContext {
  attrs: Data
  slots: Slots
  parent: ComponentInstance | null
  root: ComponentInstance
  emit: ((event: string, ...args: unknown[]) => void)
}

function setup(
  props: Data,
  context: SetupContext
): Data
```

### 2.reactive

reactive 函数接收一个对象，并返回一个对这个对象的响应式代理。它与 vue2 中的 Vue.obserable()是等价的，为避免与 RxJs 中的 observable 重名，故改名为 reactive。

```js
<template>
  <div>
    <input type="text" v-model="state.input">
    <p>input: {{state.input}}</p>
    <p>computedInput: {{state.computedInput}}</p>
  </div>
</template>
<script>
  import {reactive, computed, watch} from 'vue'

  export default {
    setup(props) {
      const state = reactive({
        input: '',
        computedInput: computed(() => '?  '+state.input+'  ?' )
      })
      return {state, singleComputed}
    }
  }
</script>
```

必须注意的是，reactive 使用者必须始终保持对返回对象的引用，以保持反应性。该对象不能被破坏或散布,所有 setup 和组合函数中不能返回 reactive 的解构。

```js
<template>
  <div>
    {{input}}
    <button @click="onClick">change</button>
  </div>
</template>
<script>
  import {reactive, computed, watch, toRefs} from 'vue'

  export default {
    setup(props) {
      const state = reactive({input: ''})

      const onClick = () => {
        state.input = '我已经改变了'
      }
      // return state 模板上下文丢失引用
      // return {...state, onClick} 模板上下文丢失引用
      return {...toRefs(state), onClick} // 正确的
    }
  }
</script>
```

如上组件如果不使用 toRefs,在点击 change 按钮时，组件并不会重新渲染，也就是说模板中的 input 还是之前的值。出现这种现象的根本原因是 js 中是值传递，并不是引用传递。解决方案是使用 toRefs，至于 ref 是什么，请看下文。

### 3.ref

ref 函数接收一个用于初始化的值并返回一个响应式的和可修改的 ref 对象。该 ref 对象存在一个 value 属性，value 保存着 ref 对象的值。

```js
<template>
  <div class="tf">
    <div>
      <!--在模板中使用时不需要使用count.value, 会自动解包-->
      <p>{{count}}</p>
      <button @click="onClick(true)">+</button>
      <button @click="onClick(false)">-</button>
    </div>
  </div>
</template>
<script>
  import {ref, reactive} from 'vue'

  export default {
    setup() {
      const count = ref(0)
      <!--在reactive中使用也不需要count.value, 也会解包-->
      const state = reactive({count})
      const onClick = (isAdd) => {
        isAdd ? count.value++ : count.value --
      }
      return {count, onClick}
    },
  }
</script>
```

_注意：在 reactive 和 tempalte 中使用的时候，不需要使用.value，它们会自动解包。_

### 4.isRef

isRef 用于判断变量是否为 ref 对象。

```js
const unwrapped = isRef(foo) ? foo.value : foo;
```

### 5.toRefs

toRefs 用于将一个 reactive 对象转化为属性全部为 ref 对象的普通对象。

```js
const state = reactive({
  foo: 1,
  bar: 2,
});
const stateAsRefs = toRefs(state);
/*
Type of stateAsRefs:
{
  foo: Ref<number>,
  bar: Ref<number>
}
*/
```

toRefs 在 setup 或者 Composition Function 的返回值特别有用：

```js
import { reactive, toRefs } from "vue";
function useFeatureX() {
  const state = reactive({
    foo: 1,
    bar: 2,
  });
  return state;
}
function useFeature2() {
  const state = reactive({
    a: 1,
    b: 2,
  });
  return toRefs(state);
}

export default {
  setup() {
    // 使用解构之后foo和bar都丧失响应式
    const { foo, bar } = useFeatureX();
    // 即便使用了解构也不会丧失响应式
    const { a, b } = useFeature2();
    return {
      foo,
      bar,
    };
  },
};
```

### 6.computed

computed 函数与 vue2 中 computed 功能一致，它接收一个函数并返回一个 value 为 getter 返回值的不可改变的响应式 ref 对象。

```js
const count = ref(1)
const plusOne = computed(() => count.value + 1)
console.log(plusOne.value) // 2
plusOne.value++ // 错误，computed不可改变

// 同样支持set和get属性
onst count = ref(1)
const plusOne = computed({
  get: () => count.value + 1,
  set: val => { count.value = val - 1 }
})
plusOne.value = 1
console.log(count.value) // 0
```

### 7.readonly

readonly 函数接收一个对象（普通对象或者 reactive 对象）或者 ref 并返回一个只读的参数对象代理，在参数对象改变时，返回的代理对象也会相应改变。如果传入的是 reactive 或 ref 响应对象，那么返回的对象也是响应的。

```js
<template>
  <!--vue3中允许template下有多个根元素-->
  <input type="text" v-model="state.count">
  <button @click="onClick">change</button>
</template>
<script>
  import {reactive, readonly, watch} from 'vue'

  export default {
    setup() {
      const state = reactive({count: 12})
      const rnState = readonly(state)
      watch(() => {
        // state改变也会触发rnState的watch
        console.log(rnState.count)
      })

      const planObj = {count: 12}
      const rnPlanObj = readonly(planObj)
      const onClick = () => {
        planObj.count = 888
        console.log(rnPlanObj.count) // 888
      }
      return {state, onClick}
    }
  }
</script>
```

### 8.watch

相比于 vue2 的 watch，Composition API 的 watch 接口不仅仅是将其逻辑抽取出来，其功能也得到了极大的丰富。下面看其类型定义：

```js
type StopHandle = () => void

type WatcherSource<T> = Ref<T> | (() => T)

type MapSources<T> = {
  [K in keyof T]: T[K] extends WatcherSource<infer V> ? V : never
}

type InvalidationRegister = (invalidate: () => void) => void

interface DebuggerEvent {
  effect: ReactiveEffect
  target: any
  type: OperationTypes
  key: string | symbol | undefined
}

interface WatchOptions {
  lazy?: boolean
  flush?: 'pre' | 'post' | 'sync'
  deep?: boolean
  onTrack?: (event: DebuggerEvent) => void
  onTrigger?: (event: DebuggerEvent) => void
}

// basic usage
function watch(
  effect: (onInvalidate: InvalidationRegister) => void,
  options?: WatchOptions
): StopHandle

// wacthing single source
function watch<T>(
  source: WatcherSource<T>,
  effect: (
    value: T,
    oldValue: T,
    onInvalidate: InvalidationRegister
  ) => void,
  options?: WatchOptions
): StopHandle

// watching multiple sources
function watch<T extends WatcherSource<unknown>[]>(
  sources: T
  effect: (
    values: MapSources<T>,
    oldValues: MapSources<T>,
    onInvalidate: InvalidationRegister
  ) => void,
  options? : WatchOptions
): StopHandle
```

- 基础用法

```js
 <template>
   <div>
     <input type="text" v-model="state.count">{{state.count}}
     <input type="text" v-model="inputRef">{{inputRef}}
   </div>
 </template>
 <script>
   import {watch, reactive, ref} from 'vue'

 export default {
 setup() {
 const state = reactive({count: 0})
 const inputRef = ref('')
 // state.count 与 inputRef 中任意一个源改变都会触发 watch
 watch(() => {
 console.log('state', state.count)
 console.log('ref', inputRef.value)
 })
 return {state, inputRef}
 }
 }
 </script>
```

- 指定依赖源

在基础用法中:

- 当依赖了多个 reactive 或 ref 是无法直接看到的，需要在回调中寻找. -在回调中我虽然使用了多个 reactive，但我希望只有在某个 reactive 改变时才触发 watch。
  解决上面两个问题可以为 watch 指定依赖源：

```js
<template>
  <div>
    state2.count: <input type="text" v-model="state2.count">
    {{state2.count}}<br/><br/>
    ref2: <input type="text" v-model="ref2">{{ref2}}<br/><br/>
  </div>
</template>
<script>
  import {watch, reactive, ref} from 'vue'

  export default {
    setup() {
      const state2 = reactive({count: ''})
      const ref2 = ref('')
      // 通过函数参数指定reative依赖源
      // 只有在state2.count改变时才会触发watch
      watch(
        () => state2.count,
        () => {
          console.log('state2.count',state2.count)
          console.log('ref2.value',ref2.value)
      })
      // 直接指定ref依赖源
      watch(ref2,() => {
        console.log('state2.count',state2.count)
        console.log('ref2.value',ref2.value)
      })

      return {state, inputRef, state2, ref2}
    }
  }
</script>
```

- watch 多个数据源

```js
<template>
  <div>
    <p>
      <input type="text" v-model="state.a"><br/><br/>
      <input type="text" v-model="state.b"><br/><br/>
    </p>
    <p>
      <input type="text" v-model="ref1"><br/><br/>
      <input type="text" v-model="ref2"><br/><br/>
    </p>
  </div>
</template>

<script>
  import {reactive, ref, watch} from 'vue'

  export default {
    setup() {
      const state = reactive({a: 'a', b: 'b'})
      // state.a和state.b任意一个改变都会触发watch的回调
      watch(() => [state.a, state.b],
       // 回调的第二个参数是对应上一个状态的值
       ([a, b], [preA, preB]) => {
        console.log('callback params:', a, b, preA, preB)
        console.log('state.a', state.a)
        console.log('state.b', state.b)
        console.log('****************')
      })

      const ref1 = ref(1)
      const ref2 = ref(2)
      watch([ref1, ref2],([val1, val2], [preVal1, preVal2]) => {
        console.log('callback params:', val1, val2, preVal1, preVal2)
        console.log('ref1.value:',ref1.value)
        console.log('ref2.value:',ref2.value)
         console.log('##############')
      })
      return {state, ref1, ref2}
    }
  }
</script>
```

- 取消 watch

watch 接口会返回一个函数，该函数用以取消 watch。

```js
<template>
  <div>
    <input type="text" v-model="inputRef">
    <button @click="onClick">stop</button>
  </div>
</template>

<script>
  import {watch, ref} from 'vue'

  export default {
    setup() {
      const inputRef = ref('')
      const stop = watch(() => {
        console.log('watch', inputRef.value)
      })
      const onClick = () => {
        // 取消watch，取消之后对应的watch不会再执行
        stop()
      }
      return {inputRef, onClick}
    }
  }
</script>
```

- 清除副作用

为什么需要清除副作用？有这样一种场景，在 watch 中执行异步操作时，在异步操作还没有执行完成，此时第二次 watch 被触发，这个时候需要清除掉上一次异步操作。

```js
<template>
  <div>
    <h3>Cleanup</h3>
    <input type="text" v-model="inputRef">
    <input type="text" v-model="inputRef2">
  </div>
</template>

<script>
  import {ref, watch} from 'vue'

  export default {
    setup() {
      const inputRef = ref('')
      const getData = (value) => {
        const handler = setTimeout(() => {
            console.log('已获得数据', value)
        }, 5000)
        return handler
      }
      watch((onCleanup) => {
        const handler = getData(inputRef.value)
        // 清除副作用
        onCleanup(() => {
          clearTimeout(handler)
        })
      })

      // 另外一种获取onCleanup的方式
      const inputRef2 = ref('')
      watch(inputRef2, (val, oldVal, onCleanup) => {
        const handler = getData(val)
        // 清除副作用
        onCleanup(() => {
          clearTimeout(handler)
        })
      })

      return {inputRef, inputRef2}
    }
  }
</script>
```

watch 提供了一个 onCleanup 的副作用清除函数，该函数接收一个函数，在该函数中进行副作用清除。那么 onCleanup 什么时候执行？

- watch 的 callback 即将被第二次执行时先执行 onCleanup。

- watch 被停止时，即组件被卸载之后。

- watch 选项

watch 接口还支持配置一些选项以改变默认行为，配置选项可通过 watch 的最后一个参数传入。

- lazy

vue2 中的 watch 默认在组件挂载之后是不会执行的，但如果希望立刻执行，可设置 immediate 为 true。而在 vue3 中，watch 默认会在组件挂载之后执行，如果希望取得与 vue2 watch 同样的行为，可配置 lazy 为 true：

```js
<template>
  <div>
     <h3>Watch Option</h3>
    <input type="text" v-model="inputRef">
  </div>
</template>
<script>
  import {watch, ref} from 'vue'

  export default {
    setup() {
      const inputRef = ref('')
      // 配置lazy为true之后，组件挂载不会执行，知道inputRef改变才执行
      watch(() => {
        console.log(inputRef.value)
      }, {lazy: true})
      return {inputRef}
    }
  }
</script>
```

- deep

如 vue2 中的 deep 参数一般，在设置 deep 为 true 之后，深层对象的任意一个属性改变都会触发回调的执行。

```js
<template>
  <div>
    <button @click="onClick">CHANGE</button>
  </div>
</template>
<script>
  import {watch, ref} from 'vue'

  export default {
    setup() {
      const objRef = ref({a: {b: 123}, c: 123})
      watch(objRef,() => {
        console.log('objRef.value.a.b',objRef.value.a.b)
      }, {deep: true})
      const onClick = () => {
        // 设置了deep之后，深层对象任意属性的改变都会触发watch回调的执行
        objRef.value.a.b = 780
      }
      return {onClick}
    }
  }
</script>
```

- flush

  当同一个 tick 中发生许多状态突变时，Vue 的反应性系统会缓冲观察者回调并异步刷新它们，以避免不必要的重复调用。默认的行为是：当调用观察者回调时，组件状态和 DOM 状态已经同步。

  这种行为可以通过 flush 来进行配置，flush 有三个值，分别是 post(默认)、pre 与 sync。sync 表示在状态更新时同步调用，pre 则表示在组件更新之前调用。

- onTrack 与 onTrigger

  onTrack 与 onTrigger 用于调试:

  - onTrack：在 reactive 属性或 ref 被追踪为依赖时调用。
  - onTrigger： 在 watcher 的回调因依赖改变而触发时调用。

```js
<template>
  <div>
    <h4>test onTrigger & onTrack</h4>
    <input type="text" v-model="debugRef">
  </div>
</template>
<script>
  import {watch, ref, reactive} from 'vue'

  export default {
    setup() {
      const debugRef = ref(0)
      // 在组件挂载之后只有onTrack被调用
      // 在debugRef改变之后先调用onTrigger，再调用onTrack
      watch(() => {
        console.log('debugRef.value:',debugRef.value)
      }, {
        onTrack() {
          debugger;
        },
        onTrigger() {
          debugger;
        }
      })
      return {inputRef, onClick, debugRef}
    }
  }
</script>
```

### 9.Lifecycle Hooks

Composition API 当然也提供了组件生命周期钩子的回调。

```js
import { onMounted, onUpdated, onUnmounted } from "vue";

const MyComponent = {
  setup() {
    onMounted(() => {
      console.log("mounted!");
    });
    onUpdated(() => {
      console.log("updated!");
    });
    onUnmounted(() => {
      console.log("unmounted!");
    });
  },
};
```

对比 vue2 的生命周期钩子：

- `beforeCreate` -> 使用 `setup()`
- `created` -> 使用 `setup()`
- `beforeMount` -> `onBeforeMount`
- `mounted` -> `onMounted`
- `beforeUpdate` -> `onBeforeUpdate`
- `updated` -> `onUpdated`
- `beforeDestroy` -> `onBeforeUnmount`
- `destroyed` -> `onUnmounted`
- `errorCaptured` -> `onErrorCaptured`

### 10.provide&inject

类似于 vue2 中 provide 与 inject, vue3 提供了对应的 provide 与 inject API。

```js
import { provide, inject } from "vue";

const ThemeSymbol = Symbol();

const Ancestor = {
  setup() {
    provide(ThemeSymbol, "dark");
  },
};

const Descendent = {
  setup() {
    const theme = inject(ThemeSymbol, "light" /* optional default value */);
    return {
      theme,
    };
  },
};
```

### 11.Template Refs

当使用前文中的 ref 时会感到迷惑，模板中也有一个 ref 用于获取组件实例或 dom 对象，这样会不会冲突。而实际上在 vue3 中，ref 会用来保存 templates 的 ref。

```js
<template>
  <div ref="root">
    Test Template Refs
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
  setup() {
    const root = ref(null)

    onMounted(() => {
      // 在render初始化之后，DOM元素将被赋值为ref
      console.log(root.value) // div dom对象
    })

    return {
      root
    }
  }
}
</script>
```

### 12.defineComponent

该接口是为了支持 ts 类型引用：

```js
import { defineComponent } from "vue";

export default defineComponent({
  props: {
    foo: String,
  },
  setup(props) {
    props.foo; // <- type: string
  },
});
```

当只有 setup 选项时：

```js
import { defineComponent } from "vue";

// provide props typing via argument annotation.
export default defineComponent((props: { foo: string }) => {
  props.foo;
});
```

**总结**

总的来说，vue3 的 Composition API 为开发者提供了更大的灵活，但更大的灵活需要更大的规则，对编程者的代码素质有一定的要求，一定程度上增加了 vue 的入门难度，另外为了持有原始类型的引用引入了 ref，引入了一些复杂度，但相比于 Composition API 所产生的效益，这些微不足道。

**参考学习文档**

[Composition API RFC](https://vue-composition-api-rfc.netlify.com/#summary)
