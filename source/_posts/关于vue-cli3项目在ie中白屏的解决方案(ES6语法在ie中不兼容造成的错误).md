---
title: 关于vue-cli3项目在ie中白屏的解决方案(ES6语法在ie中不兼容造成的错误)
date: 2020-01-09
tags: [前端, 白屏, ie]
---

处理 ie 浏览器的兼容问题是最难受的，

这次在 ie 上出现白屏的问题主要是由于 ES6 语法在浏览器中不兼容导致的。

**以下是解决方案：**

<!-- more -->

1.安装支持包

```javascript
  npm install --save babel-polyfill
```

```javascript
  npm install es6-promise --save
```

2.在大目录的 main.js 里边引用

```javascript
import "@babel/polyfill";
import Es6Promise from "es6-promise";
Es6Promise.polyfill();
```

3.还是你的大目录 vue.config.js 中插入代码，没有就新建（和 package.json 同级）

```javascript
module.exports = {
  chainWebpack: (config) => {
    config.module
      .rule("iview")
      .test(/iview.src.*?js$/)
      .use("babel")
      .loader("babel-loader")
      .end();
  },
};
```

4.还是你的大目录 babel.config.js 加上（和 vue.config.js 同级）

```javascript
module.exports = {
  presets: [
    [
      "@vue/app",
      {
        useBuiltIns: "entry",
        polyfills: ["es6.promise", "es6.symbol"],
      },
    ],
  ],
};
```
