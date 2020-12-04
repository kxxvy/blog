---
title: vue调用微信jssdk实现微信分享功能
date: 2020-03-05
tags: [前端,微信分享]
---

### 一、公众号配置

如果你们的公众号有专人保管，那么跟他说把安全域名加一下，安全域名用于微信的验证，没有这一步操作，下面的都白搭。比如我们的测试公众号，绑定的就是我们测试服务器的域名。同理，生产也是如此。

### 二、后端配置VUE

要想使用微信的JS-SDK功能，需要生成签名，配合appId才能使用，这些步骤通常是由后端生成的。这里省去3000字描述如何生成签名，反正你找后端同学就对了。

### 三、前端配置

终于轮到我们前端上场了，啰嗦了那么多，下面让我们正式起飞。

<!-- more -->

1.安装sdk

```javascript
  npm install weixin-js-sdk --save
```

2.封装微信分享功能-----新建sdk.js文件

```javascript
  import wx from 'weixin-js-sdk'    //微信sdk依赖
  import axios from 'axios';       // 引用全局
  const jsApiList = ['onMenuShareAppMessage', 'onMenuShareTimeline', 'onMenuShareQQ','onMenuShareWeibo']
  //要用到微信API
  function getJSSDK(url,dataForWeixin) {
    // 调用后台接口换取参数
    axios.get(process.env.BASE_API + 'xxxxxxxx', {
      params: {
        url: url
      },
    }).then(res => {
      console.log(res.data.data);
      wx.config({
        debug: false, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
        appId: res.data.data.appId, // 必填，公众号的唯一标识
        timestamp: res.data.data.timestamp, // 必填，生成签名的时间戳
        nonceStr: res.data.data.nonceStr, // 必填，生成签名的随机串
        signature: res.data.data.signature, // 必填，签名
        jsApiList: jsApiList // 必填，需要使用的JS接口列表
      })
      wx.ready(function () {
        wx.onMenuShareAppMessage({
          title: dataForWeixin.title,
          desc: dataForWeixin.desc,
          link: dataForWeixin.linkurl,
          imgUrl: dataForWeixin.img,
          trigger: function trigger(res) { },
          success: function success(res) {
            console.log('已分享');
          },
          cancel: function cancel(res) {
            console.log('已取消');
          },
          fail: function fail(res) {
            alert(JSON.stringify(res));
          }
        });
        // 2.2 监听“分享到朋友圈”按钮点击、自定义分享内容及分享结果接口
        wx.onMenuShareTimeline({
          title: dataForWeixin.title,
          link: dataForWeixin.linkurl,
          imgUrl: dataForWeixin.img,
          trigger: function trigger(res) {
            // alert('用户点击分享到朋友圈');
          },
          success: function success(res) {
            //alert('已分享');
          },
          cancel: function cancel(res) {
            //alert('已取消');
          },
          fail: function fail(res) {
            alert(JSON.stringify(res));
          }
        });
        // 2.3 监听“分享到QQ”按钮点击、自定义分享内容及分享结果接口
        wx.onMenuShareQQ({
          title: dataForWeixin.title,
          desc: dataForWeixin.desc,
          link: dataForWeixin.linkurl,
          imgUrl: dataForWeixin.img,
          trigger: function trigger(res) {
            //alert('用户点击分享到QQ');
          },
          complete: function complete(res) {
            alert(JSON.stringify(res));
          },
          success: function success(res) {
            //alert('已分享');
          },
          cancel: function cancel(res) {
            //alert('已取消');
          },
          fail: function fail(res) {
            //alert(JSON.stringify(res));
          }
        });
        // 2.4 监听“分享到微博”按钮点击、自定义分享内容及分享结果接口
        wx.onMenuShareWeibo({
          title: dataForWeixin.title,
          desc: dataForWeixin.desc,
          link: dataForWeixin.linkurl,
          imgUrl: dataForWeixin.img,
          trigger: function trigger(res) {
            //alert('用户点击分享到微博');
          },
          complete: function complete(res) {
            // alert(JSON.stringify(res));
          },
          success: function success(res) {
            //alert('已分享');
          },
          cancel: function cancel(res) {
            // alert('已取消');
          },
          fail: function fail(res) {
            // alert(JSON.stringify(res));
            console.log(JSON.stringify(res));
          }
        });
      })
      wx.error(function (res) {
        console.log(JSON.stringify(res)+"微信验证失败");
        // alert(JSON.stringify(res)+"微信验证失败");
      });
    })
  }
  export default {
    // 获取JSSDK
    getJSSDK: getJSSDK
  }
```

3.在页面中调用

+ 先页面中先引入(找到新建的sdk.js的位置)

```javascript
  import sdk from '../../js/sdk' //引入sdk.js
```

+ 先判断是否是微信浏览器（如果是再去调用）

+ url如果是history模式要去掉#之后的链接，如果不是直接用location.href

```javascript
  let url = location.href.split('#')[0];
  let dataForWeixin = {
    title: 'XXXXXX',    //分享标题
    desc: "XXXX",      //分享内容
    linkurl: "XXXXX", //分享链接
    img: 'http://XXXXX',     //分享内容显示的图片(图片必须是正方形的链接)
  };
  let ua = window.navigator.userAgent.toLowerCase();
  if (ua.match(/MicroMessenger/i) == 'micromessenger') {
    sdk.getJSSDK(detailCode,url);    //传入sdk.js需要的参数
  } else {
    console.log('不是微信浏览器')
  }
```

以上。
