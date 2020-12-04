---
title: vue-quill-editor 控制图片大小
date: 2020-05-28
tags: [前端,vue-quill-editor]
---
## 一、vue-quill-editor的安装和使用

1.安装vue-quill-editor富文本编辑器：npm install vue-quill-editor --save

2.在main.js中引入：

```javascript
  import VueQuillEditor from 'vue-quill-editor'
  Vue.use(VueQuillEditor)
```

<!-- more -->

3.在要使用的组件中又引入：

```javascript
  import 'quill/dist/quill.core.css'
  import 'quill/dist/quill.snow.css'
  import 'quill/dist/quill.bubble.css'
  import Quill from 'quill'
  import { quillEditor } from 'vue-quill-editor'
```

4.然后在template中使用：

```html
  <quill-editor
    v-model="content" //双向绑定的值
    ref="myQuillEditor"
    :options="editorOption"
    @blur="onEditorBlur($event)"
    @focus="onEditorFocus($event)"
    @change="onEditorChange($event)"
  >
  </quill-editor>
```

5.在script中加入：

```javascript
  components: {
    quillEditor
  },
  editorOption: {
    modules: {
      toolbar: [
        ['bold', 'italic', 'underline', 'strike', 'image'],
        ['formula', 'clean'],
        ['blockquote', 'code-block'],
        [{'list': 'ordered'}, {'list': 'bullet'}],
        [{'script': 'sub'}, {'script': 'super'}],
        [{'size': ['small', false, 'large', 'huge']}],
        [{ 'font': fonts }],
        [{'header': [1, 2, 3, 4, 5, 6, false]}],
        [{ 'color': [] }, { 'background': [] }],
        [{ 'align': [] }],
        [{'direction': 'rtl'}]
      ]
    },
    placeholder: '输入内容........'
  }
```

## 二、quill-image-resize-module的安装和使用

> 它是vue-quill-editor的一个插件，能够调整vue-quill-editor中插入的图片，非常实用。

1.安装：npm install quill-image-resize-module --save

2.在原本的组件中引入：

```javascript
  import 'quill/dist/quill.core.css'
  import 'quill/dist/quill.snow.css'
  import 'quill/dist/quill.bubble.css'
  import Quill from 'quill'
  import { quillEditor } from 'vue-quill-editor'
  import ImageResize from 'quill-image-resize-module'  //添加
  Quill.register('modules/imageResize', ImageResize)  //添加
```

3.在原本的script中添加：

```javascript
  editorOption: {
    modules: {
      imageResize: {   //添加
        displayStyles: {   //添加
          backgroundColor: 'black',
          border: 'none',
          color: 'white'
        },
        modules: ['Resize', 'DisplaySize', 'Toolbar']   //添加
      },
      toolbar: [
        ['bold', 'italic', 'underline', 'strike', 'image'],
        ['formula', 'clean'],
        ['blockquote', 'code-block'],
        [{'list': 'ordered'}, {'list': 'bullet'}],
        [{'script': 'sub'}, {'script': 'super'}],
        [{'size': ['small', false, 'large', 'huge']}],
        [{ 'font': fonts }],
        [{'header': [1, 2, 3, 4, 5, 6, false]}],
        [{ 'color': [] }, { 'background': [] }],
        [{ 'align': [] }],
        [{'direction': 'rtl'}]
      ]
    },
    placeholder: '输入内容........'
  }
```

5.还有非常重要的一步，就是要进行相关配置

### vue-cli2.0

去build文件夹下的webpack.dev.conf.js文件里

在plugins[]里面加上

```javascript
  new webpack.ProvidePlugin({
    'window.Quill': 'quill/dist/quill.js',
    'Quill': 'quill/dist/quill.js'
  }),
```

### vue-cli3.0

自己创建一个vue.config.js文件（和src同级别的）

```javascript
  const webpack = require('webpack')
  module.exports = {
    chainWebpack: config => {
      config.plugin('provide').use(webpack.ProvidePlugin, [{
        'window.Quill': 'quill/dist/quill.js',
        'Quill': 'quill/dist/quill.js'
      }]);
    }
  }
```

以上。
