---
title: WEB网页实时显示海康摄像头监控画面
date: 2021-12-12
tags: [直播]
---

最近在做个体育考试系统，

有个需求，

需要将用户运动时的画面实时显示在我们的网页上，

感觉这和直播技术是相关的，

然后就上网学习了一下，

最终得出了如下几个方案

<!-- more -->

## 一、使用 jsmpeg 拉流做视频直播

先引入`jsmpeg.min.js`，

视频流后台会处理，我们前端只需连接后台提供的 socket 地址就行

代码如下：

```js
<template>
  <div class="myVideo">
    <canvas id="video-canvas" style="height: 590px;"></canvas>
  </div>
</template>

<script>
  export default {
    name: '',
    components: {},
    data () {
      return {
        path:'ws://....',   //你拉取视频源地址
      }
    },
    methods: {
    },
    mounted () {
      var canvas = document.getElementById('video-canvas')
      var player = new JSMpeg.Player(this.path, {canvas: canvas})
    },
  }
</script>

<style lang="less">

</style>
```

**结果**

虽然视频可以正常跑起来，但是还是有问题的，

- 延迟比较高

- 卡顿特别严重，体验极差

- 在使用中，偶尔也会出现崩溃的情况

后台说是由于受限于公司的服务器和带宽，所以会存在这些问题，

作罢，那只能在尝试一下别的方案了

## 二、使用 activeX 控件

后来了解到，在 ie 浏览器上可以使用 activeX 控件，

可以实现低延迟视频显示，

不过该方案只能在 ie 浏览器上运行，

所以需要先对项目进行 ie 的兼容（真是难受= =）

只能硬着头皮上了

1、先下载插件

2、页面调用 ActiveX，写法

```html
// clsid可通过在ie浏览器的管理下载项查看得到

<object
  classid="clsid:控件识别号"
  id="自定义id"
  codebase="plugins(路径)/控件文件名#version=3,4,0,0"
></object>
```

**结果**

视频也正常跑起来了，而且延迟大概在 2s 左右，已经是很不错了

但是唯一的问题就是该视频是由插件展示，

对其可扩展性和可操作性极低，

如果页面只是展示这一个视频画面的话，

其实是可以接受的，

另一方面就是在 ie 浏览器中页面偶尔也会崩坏掉

遂放弃~

## 三、rtsp 流 转 http 播放视频

1、打开 vlc 播放器 点击媒体菜单 选择打开网络串流

![1.png](https://s2.loli.net/2021/12/12/RIejCKDhEcmZ18p.png)

2 输入 RTSP 播放地址

![2.png](https://s2.loli.net/2021/12/12/vEyKOwUHz9PulSI.png)

3、点击播放右下角箭头选择串流

4、修改为 HTTP，点击添加

![3.png](https://s2.loli.net/2021/12/12/KT31R7Cifzgkra6.png)

5、设置请求端口和路径

![4.png](https://s2.loli.net/2021/12/12/8g5IkJeAHaFOCYy.png)

6、选择输出格式

![5.png](https://s2.loli.net/2021/12/12/INjeh5A2x3iF9mD.png)

![6.png](https://s2.loli.net/2021/12/12/lbKHF5DWLtJfZoi.png)

完成后即可使用 H5video 标签播放

```js
<!DOCTYPE HTML>
<html>
<body>

<video src="http://192.168.11.13:8080/test" controls="controls">
your browser does not support the video tag
</video>

</body>
</html>
```

**结果**

该方案总体上还是不错的，

能在谷歌上运行，

视频流畅，不卡顿，

唯一的缺点就是延迟比较高，

实测大概有 8s 左右的延迟，

## 四、vlc

后来了解到 vlc 插件在页面展示视频画面的另一种方案

1、先下载 vlc - [http://www.videolan.org/vlc/](http://www.videolan.org/vlc/) 开源的好东西，并安装

2、实时播放代码如下

```js
<object
  type="application/x-vlc-plugin"
  pluginspage="http://www.videolan.org/"
  id="vlc"
  events="false"
  width="720"
  height="410"
>
  <param name="mrl" value="rtsp://xxx" />
  <param name="volume" value="50" />
  <param name="autoplay" value="true" />
  <param name="loop" value="false" />
  <param name="fullscreen" value="false" />
  <param name="controls" value="false" />
</object>
```

**结果**

经测试，该方案可行，

谷歌浏览器可正常播放，

视频运行流畅，

虽也有延迟，但问题不大

应该是当前最可行的方案

后发现我们的硬件设备跟不上，

无法在我们的硬件设备上运行（奔溃~）

## 五、总结

因为受限于上述诸多因素，

所以我们并没有采用以上任一方案，

经过开会讨论，

最终我们以图片的形式代替视频展示的方案，

以每秒 10 帧的图片形式代替视频画面显示，

虽不尽人意，但也是目前最合理的一种方案了。

以上。
