---
title: VS Code添加git bash终端
date: 2022-21-03
tags: [vscode]
---

在写 `node` 时，

发现 `powershell` 似乎不能识别 `shebang`，

而 `git bash` 却能正常运行，

所以打算在 `vscode` 中添加 `git bash` 终端，

<!-- more -->

1、打开 vscode

2、文件->首选项->设置，打开设置

3、搜索`shell windows`

![bash_set.png](https://s2.loli.net/2022/01/03/qp2oAemUXlCkabf.png)

4、打开 settings.json 在里面输入代码

```js
"terminal.integrated.profiles.windows": {
  "gitBash": {
    "path": "D:\\Program Files\\Git\\bin\\bash.exe", // 这里是bash的路径
  }
},
"terminal.integrated.defaultProfile.windows": "gitBash"
```

5、保存重启 vscode，按 ctrl+~键打开终端，完成
