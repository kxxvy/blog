---
title: 「Fira Code」字体
date: 2021-03-27
tags: [字体]
---

## 修改字体，使用「Fira Code」字体

发现一款字体很漂亮，很适合用来写代码：

<!-- more -->

![687474703a2f2f696d672e736d79687661652e636f6d2f32303230303531365f313633332e706e67.png](https://i.loli.net/2021/05/23/mSe8B4156RnVxsY.png)

安装步骤如下：

（1）进入 [https://github.com/tonsky/FiraCode](https://github.com/tonsky/FiraCode) 网站，下载并安装「Fira Code」字体。

（2）打开 VS Code 的「设置」，搜索font，修改相关配置为如下内容：

```
"editor.fontFamily": "'Fira Code',Menlo, Monaco, 'Courier New', monospace", // 设置字体显示
"editor.fontLigatures": false,//控制是否启用字体连字，true启用，false不启用
```

上方的第二行为字体连体配置，可根据个人习惯开启。
