---
title: craco的使用
date: 2020-03-28
tags: [前端,React]
---

## 一、认识craco

有时我们对主题等相关的高级特性进行配置，好像需要修改create-react-app 的默认配置。

如何修改create-react-app 的默认配置呢？

<!-- more -->

+ 可以通过yarn run eject来暴露出来对应的配置信息进行修改
+ 但是对于webpack并不熟悉的人来说，直接修改 CRA 的配置是否会给你的项目带来负担，甚至会增加项目的隐患和不稳定性
+ 所以，在项目开发中是不建议大家直接去修改 CRA 的配置信息的

那么如何来进行修改默认配置呢？社区目前有两个比较常见的方案：

+ react-app-rewired + customize-cra；（这个是antd早期推荐的方案）
+ craco（目前antd推荐的方案）

## 二、Craco的使用步骤

第一步：安装craco：

```javascript
  yarn add @craco/craco
```

第二步：修改package.json文件

+ 原本启动时，我们是通过react-scripts来管理的
+ 现在启动时，我们通过craco来管理

```javascript
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",

    "start": "craco start", // 修改
    "build": "craco build",  // 修改
    "test": "craco test",  // 修改
  }
```

第三步：在根目录下创建craco.config.js文件用于修改默认配置

```javascript
  module.exports = {
    // 配置文件
  }
```

按照配置主题的要求，自定义主题需要用到类似 less-loader 提供的 less 变量覆盖功能：

+ 我们可以引入 craco-less 来帮助加载 less 样式和修改变量

安装 craco-less：

```javascript
  yarn add craco-less
```

修改craco.config.js中的plugins：

+ 使用modifyVars可以在运行时修改LESS变量；

```javascript
  const CracoLessPlugin = require('craco-less');
    module.exports = {
      plugins: [
      {
      plugin: CracoLessPlugin,
        options: {
          lessLoaderOptions: {
            lessOptions: {
              modifyVars: { '@primary-color': '#1DA57A' },
              javascriptEnabled: true,
            },
          },
        },
      },
    ],
  }
```

引入antd的样式时，引入antd.less文件：

```javascript
  import 'antd/dist/antd.less';
```

修改后重启 yarn start, 这样就配置成功了
