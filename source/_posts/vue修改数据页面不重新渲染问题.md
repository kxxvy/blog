---
title: vue修改数据页面不重新渲染问题
date: 2020-02-25
tags: [前端,vue]
---
使用vue，遇到几次修改了对象的属性后，页面并不重新渲染，场景如下：

<!-- more -->

```html
  <template  v-for="item in tableData">
    <div :class="{'redBorder':item.red}">
      <div>{{ item.name}}</div>
      <div>
        <el-button size="mini" @click="clickBtn(item.id)" type="info">编辑</el-button>
          <p class="el-icon-error" v-show="item.tip"></p>
      </div>
    </div>
  </template>
```

js部分如下：

```javascript
  <script>
    export default {
      data() {
        return {
        tableData:[{id:0,name:"lili",red:false,tip:false}]
        }
      },
  
      methods: {
        clickBtn(id){
          this.tableData[id].red=true;
          this.tableData[id].tip=true;
        }
      }
    }
  </script>
```

在项目中点击button后不出现红色边框和提示错误框，打开debugger查看，发现运行到了这里却没有执行，tableData中的值并没有改变，这个方法在以前使用时会起作用，可能是这次的项目比较复杂引起的，具体原因不明。
后通过查找资料修改为使用$set来设定修改值，js如下：

```javascript
  this.$set(this.tableData[id],"red",true);
```

### 总结

之后研究了一下发现一般有两种情况下页面不会渲染：

+第一种情况：就是在初始化的时候没有这个属性，是动态添加的属性。这个时候不会引起vue自动渲染机制。

+第二种情况：在操作数组的时候，要用push 或者 splice 等 可以改变这种方法改变原数组。而不是用下标 this.mydata[0] = '改变的值'。这样也会引起不渲染。

根据官方文档定义：如果在实例创建之后添加新的属性到实例上，它不会触发视图更新。

当你把一个普通的 JavaScript 对象传入 Vue 实例作为 data 选项，Vue 将遍历此对象所有的属性，并使用 Object.defineProperty 把这些属性全部转为 getter/setter。

受现代 JavaScript 的限制 (以及废弃 Object.observe)，Vue 不能检测到对象属性的添加或删除。由于 Vue 会在初始化实例时对属性执行 getter/setter 转化过程，所以属性必须在 data 对象上存在才能让 Vue 转换它，这样才能让它是响应的。

**解决方案**：

Vue 不允许在已经创建的实例上动态添加新的根级响应式属性 (root-level reactive property)。然而它可以使用 Vue.set(object, key, value) 方法将响应属性添加到嵌套的对象上：

```javascript
  this.$set(object, key, data);
```

如果情况比较复杂，所有方法都试过了还没有解决，可以用 v-if 强制重新渲染更新。
