---
title: 关于vue-cli3项目在ie中白屏的解决方案(ES6语法在ie中不兼容造成的错误)
date: 2020-01-09
tags: [前端,白屏,ie]
---
作为一个前端菜鸟，每次碰到ie都有说不出的痛，

这次在ie上出现白屏的问题主要是由于ES6语法在浏览器中不兼容导致的。

**以下是解决方案：**

<!-- more -->

1.安装支持包

```javascript
  npm install --save babel-polyfill
```

```javascript
  npm install es6-promise --save
```

2.在大目录的main.js里边引用

```javascript
  import '@babel/polyfill';
  import Es6Promise from 'es6-promise'
  Es6Promise.polyfill()
```

3.还是你的大目录vue.config.js中插入代码，没有就新建（和package.json同级）

```javascript
  module.exports = {
  chainWebpack: config => {
      config.module
        .rule('iview')
        .test(/iview.src.*?js$/)
        .use('babel')
          .loader('babel-loader')
          .end()
    },
  }
```

4.还是你的大目录babel.config.js加上（和vue.config.js同级）

```javascript
  module.exports = {
    presets: [
      [
        "@vue/app",
        {
          "useBuiltIns": "entry",
          polyfills: [
            'es6.promise',
            'es6.symbol'
          ]
        }
      ]
    ],
  };
```
