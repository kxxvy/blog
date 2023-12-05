---
title: App内嵌H5页面数据如何交互
date: 2023-04-08
tags: [webview]
---

## 背景

在做原生与 h5 混合开发的项目时，

web 和 native 之间需要数据交互，

因此需要一个连接的桥梁

<!-- more -->

## WebViewJavaScriptBridge

我们使用的方案是`WebViewJavaScriptBridge`, 简称【JS Bridge】

一、创建 bridge.js 文件用于封装 `WebViewJavaScriptBridge` （src/config/bridge.js)

```js
function setupWebViewJavascriptBridge(callback) {
  if (window.WebViewJavascriptBridge) {
    return callback(window.WebViewJavascriptBridge);
  }
  if (window.WVJBCallbacks) {
    return window.WVJBCallbacks.push(callback);
  }
  window.WVJBCallbacks = [callback];
  let WVJBIframe = document.createElement("iframe");
  WVJBIframe.style.display = "none";
  WVJBIframe.src = "https://__bridge_loaded__";
  document.documentElement.appendChild(WVJBIframe);
  setTimeout(() => {
    document.documentElement.removeChild(WVJBIframe);
  }, 0);
}
export default {
  callhandler(name, data, callback) {
    setupWebViewJavascriptBridge(function (bridge) {
      bridge.callHandler(name, data, callback);
    });
  },
  registerhandler(name, callback) {
    setupWebViewJavascriptBridge(function (bridge) {
      bridge.registerHandler(name, function (data, responseCallback) {
        callback(data, responseCallback);
      });
    });
  },
};
```

二、main.js 中引入 bridge.js 文件，并挂载 vue 原型上，每个 vue 实例中即可以使用

```js
import Bridge from "./config/bridge.js";
vue.prototype.$bridge = Bridge;
```

三、在需要调用客户端方法的组件中

```js
this.$bridge.callhandler("方法名", params, (data) => {
  // 处理返回数据
});
```

四、客户端需要调用 js 函数时，事先注册约定好的函数即可

```js
this.$bridge.registerhandler("方法名", (data, responseCallback) => {
  console.log(data);
  responseCallback(data);
});
```
