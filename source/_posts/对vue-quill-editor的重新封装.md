---
title: 对vue-quill-editor的重新封装
date: 2020-05-24
tags: [前端,vue-quill-editor]
---

## 需求概述

vue-quill-editor是我们再使用vue框架的时候常用的一个富文本编辑器，在进行富文本编辑的时候，我们往往要插入一些图片，vue-quill-editor默认的处理方式是直接将图片转成base64编码，这样的结果是整个富文本的html片段十分冗余，通常来讲，每个服务器端接收的post的数据大小都是有限制的，这样的话有可能导致提交失败，或者是用户体验很差，数据要传递很久才全部传送到服务器。
因此，在富文本编辑的过程中，对于图片的处理，我们更合理的做法是将图片上传到服务器，再将图片链接插入到富文本中，以达到最优的体验。
废话不多说，接下来直接看如何改造。

<!-- more -->

### 引入element-ui

为了更好的控制上传的图片，我这里引用了element-ui的上传图片组件

```html
  <template>
    <div>
      <!-- 图片上传组件辅助-->
      <el-upload
        class="avatar-uploader"
        :action="serverUrl"
        name="img"
        :headers="header"
        :show-file-list="false"
        :on-success="uploadSuccess"
        :on-error="uploadError"
        :before-upload="beforeUpload">
      </el-upload>
    </div>
  </template>
  <script>
    export default {
      data() {
        return {
          serverUrl: '',  // 这里写你要上传的图片服务器地址
          header: {token: sessionStorage.token}  // 有的图片服务器要求请求头需要有token  
        }
      },
      methods: {
        // 上传图片前
        beforeUpload(res, file) {},
        // 上传图片成功
        uploadSuccess(res, file) {},
        // 上传图片失败
        uploadError(res, file) {}
      }
    }
  </script>
```

这里要使用element-ui主要有2个好处

+ 可以对图片上传前，图片上传成功，图片上传失败等情况进行操作，也就是代码中的

```html
  :on-success="uploadSuccess"  //  图片上传成功
  :on-error="uploadError"  // 图片上传失败
  :before-upload="beforeUpload"  // 图片上传前
```

+ 使用element-ui的v-loading显示loading动画

### 引入vue-quill-editor

```html
  <template>
    <div>
      <!-- 图片上传组件辅助-->
      <el-upload
        class="avatar-uploader"
        :action="serverUrl"
        name="img"
        :headers="header"
        :show-file-list="false"
        :on-success="uploadSuccess"
        :on-error="uploadError"
        :before-upload="beforeUpload">
      </el-upload>
        <!--富文本编辑器组件-->
      <el-row v-loading="uillUpdateImg">
        <quill-editor
          v-model="detailContent"
          ref="myQuillEditor"
          :options="editorOption"
          @change="onEditorChange($event)"
          @ready="onEditorReady($event)"
        >
        </quill-editor>
      </el-row>
    </div>
  </template>
  <script>
    export default {
      data() {
        return {
          quillUpdateImg: false, // 根据图片上传状态来确定是否显示loading动画，刚开始是false,不显示
          serverUrl: '',  // 这里写你要上传的图片服务器地址
          header: {token: sessionStorage.token},  // 有的图片服务器要求请求头需要有token之类的参数，写在这里
          detailContent: '', // 富文本内容
          editorOption: {}  // 富文本编辑器配置
        }
      },
      methods: {
        // 上传图片前
        beforeUpload(res, file) {},
        // 上传图片成功
        uploadSuccess(res, file) {},
        // 上传图片失败
        uploadError(res, file) {}
      }
    }
  </script>
```

这里可以看到我们用一个包裹我们的富文本组件，是为了使用loading动画，就是v-loading这个设置

### 重写点击图片按钮事件

```javascript
  export default {
    data() {
      return {
        serverUrl: '',  // 这里写你要上传的图片服务器地址
        header: {token: sessionStorage.token},  // 有的图片服务器要求请求头需要有token之类的参数，写在这里
        detailContent: '', // 富文本内容
        editorOption: {
          placeholder: '',
          theme: 'snow',  // or 'bubble'
          modules: {
            toolbar: {
              container: toolbarOptions,  // 工具栏
              handlers: {
                'image': function (value) {
                  if (value) {
                      document.querySelector('#quill-upload input').click()
                  } else {
                      this.quill.format('image', false);
                  }
                }
              }
            }
          }
        }
      }
    }
  }
```

配置中的handlers是用来定义自定义程序的，然而我们配置完后会懵逼地发现，整个富文本编辑器的工具栏的图片上传等按钮都不见了 只保留了几个基本的富文本功能。

这个是因为添加自定义处理程序将覆盖默认的工具栏和主题行为
因此我们要再自行配置下我们需要的工具栏，所有功能的配置如下，大家可以按需配置

```javascript
  <script>
    // 工具栏配置
    const toolbarOptions = [
      ['bold', 'italic', 'underline', 'strike'],        // toggled buttons
      ['blockquote', 'code-block'],

      [{'header': 1}, {'header': 2}],               // custom button values
      [{'list': 'ordered'}, {'list': 'bullet'}],
      [{'script': 'sub'}, {'script': 'super'}],      // superscript/subscript
      [{'indent': '-1'}, {'indent': '+1'}],          // outdent/indent
      [{'direction': 'rtl'}],                         // text direction

      [{'size': ['small', false, 'large', 'huge']}],  // custom dropdown
      [{'header': [1, 2, 3, 4, 5, 6, false]}],

      [{'color': []}, {'background': []}],          // dropdown with defaults from theme
      [{'font': []}],
      [{'align': []}],
      ['link', 'image', 'video'],
      ['clean']                                         // remove formatting button
    ]

  export default {
    data() {
      return {
        editorOption: {
          placeholder: '',
          theme: 'snow',  // or 'bubble'
          modules: {
            toolbar: {
              container: toolbarOptions,  // 工具栏
              handlers: {
                'image': function (value) {
                  if (value) {
                    alert(1)
                  } else {
                    this.quill.format('image', false);
                  }
                }
              }
            }
          }
        }
      }
    }
  }
</script>
```

由于这里的工具栏配置列举了所有，看起来很长一堆，我建议大家可以写在单独一个文件，然后再引入，美观一点

### 自定义按钮事件打开上传图片

接下来在handles里面继续完善我们的图片点击事件。

+ 第一步，点击按钮选择本地图片

```javascript
  handlers: {
    'image': function (value) {
      if (value) {
      // 触发input框选择图片文件
        document.querySelector('.avatar-uploader input').click()
        } else {
        this.quill.format('image', false);
      }
    }
  }
```

在这里我们的自定义事件就结束了，接下来图片上传成功或者失败都由

```html
  :on-success="uploadSuccess"  //  图片上传成功
  :on-error="uploadError"  // 图片上传失败
  :before-upload="beforeUpload"  // 图片上传前
```

这三个函数来处理

```javascript
  // 富文本图片上传前
  beforeUpload() {
    // 显示loading动画
    this.quillUpdateImg = true
  },
  
  uploadSuccess(res, file) {
    // res为图片服务器返回的数据
    // 获取富文本组件实例
    let quill = this.$refs.myQuillEditor.quill
    // 如果上传成功
    if (res.code === '200' && res.info !== null) {
        // 获取光标所在位置
        let length = quill.getSelection().index;
        // 插入图片  res.info为服务器返回的图片地址
        quill.insertEmbed(length, 'image', res.info)
        // 调整光标到最后
        quill.setSelection(length + 1)
    } else {
        this.$message.error('图片插入失败')
    }
    // loading动画消失
    this.quillUpdateImg = false
  },

  // 富文本图片上传失败
  uploadError() {
    // loading动画消失
    this.quillUpdateImg = false
    this.$message.error('图片插入失败')
  }
```

以上。
