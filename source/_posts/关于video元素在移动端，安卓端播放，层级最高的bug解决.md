---
title: 关于video元素在移动端，安卓端播放，层级最高的bug解决
date: 2021-04-11
tags: [video]
---

最近在做项目时发现video标签在移动端有一些兼容性的问题，特此记录一下。

<!-- more -->

video元素在安卓端播放视频，层级最高，遮挡弹窗，百度一堆都是说加x5-video-player-type="h5"属性，其实不然，正确的写法是

```html
 <video
  :id="`video${index}`"
  loop
  objectfit="fill"
  preload="auto"
  x5-video-player-type="h5-page"
  webkit-playsinline="true"
  playsinline="true"
  :poster="item.cover_img_url"
  @click="pauseVideo"
  controlslist="nodownload nofullscreen"
  disablePictureInPicture
>
  <source :src="item.video_url" type="video/mp4" />
</video>
```

百度一堆叫你加x5-playsinline ，会导致在x5内核浏览器中播放层级最高，去掉完美解决，经过测试ios和安卓perfect。
