---
title: vue+px2rem实现pc端大屏自适应(rem适配)
date: 2020-01-15
tags: [前端,适配]
---

### 配置前言

+ 项目构建：基于vue-cli3构建，使用postcss-px2rem px2rem-loader进行rem适配

+ 实现原理：每次打包，webpack通过使用插件postcss-px2rem，帮我们自动将px单位转换成rem单位

+ 前方有坑：UI框架部分组件使用JavaScript将css作为内联样式直接写在html标签内，打包适配时不会读取相关css,所以要配置相关样式，在style中需要" !important "进行样式覆盖。

<!-- more -->

### 第一步，安装postcss-px2rem及px2rem-loader

打开命令行工具，输入以下指令安装插件(当然用淘宝镜像cnpm安装会更快)

```javascript
  npm install postcss-px2rem px2rem-loader --save
```

### 第二步，在根目录src中新建util目录下新建rem.js等比适配文件

```javascript
  // rem等比适配配置文件
  // 基准大小
  const baseSize = 16
  // 设置 rem 函数
  function setRem () {
    // 当前页面宽度相对于 1920宽的缩放比例，可根据自己需要修改。
    const scale = document.documentElement.clientWidth / 1920
    // 设置页面根节点字体大小（“Math.min(scale, 2)” 指最高放大比例为2，可根据实际业务需求调整）
    document.documentElement.style.fontSize = baseSize * Math.min(scale, 2) + 'px'
  }
  // 初始化
  setRem()
  // 改变窗口大小时重新设置 rem
  window.onresize = function () {
    setRem()
  }
```

### 第三步，在main.js中引入适配文件

```javascript
  import './util/rem'
```

### 第四步，到vue.config.js中配置插件

```javascript
  // 引入等比适配插件
  const px2rem = require('postcss-px2rem')

  // 配置基本大小
  const postcss = px2rem({
    // 基准大小 baseSize，需要和rem.js中相同
    remUnit: 16
  })

  // 使用等比适配插件
  module.exports = {
    lintOnSave: true,
    css: {
      loaderOptions: {
        postcss: {
          plugins: [
            postcss
          ]
        }
      }
    }
  }
```
