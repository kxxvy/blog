---
title: npm发布一个脚手架库的时候报错以及解决方法
date: 2022-01-10
tags: [npm]
---

今天本打算把自己开发的脚手架工具发布到 `npm` 上，

`npm login` 之后，

报如下错误

<!-- more -->

> npm notice Beginning October 4, 2021, all connections to the npm registry - including for package installation - must use TLS 1.2 or higher. You are currently using plaintext http to connect. Please visit the GitHub blog for more information: `https://github.blog/2021-08-23-npm-registry-deprecating-tls-1-0-tls-1-1/`
> npm ERR! code E426 npm ERR! 426 Upgrade Required - PUT `http://registry.npmjs.org/my-lib`

大意是，从 2021 年 10 月 4 日开始，所有前往 `npm registry` 的连接，都需要使用 `TLS 1.2` 了.

因此，我把命名行改成使用 `https`，旧的错误消息就消失了：

```bash
npm config set registry=https://registry.npmjs.org
```

然而我又遇到了新的错误消息：you do not have permission to publish "kv-cli". Are you logged in as the correct user?

> npm ERR! code E403
> npm ERR! 403 403 Forbidden - PUT `https://registry.npmjs.org/my-lib` - You do not have permission to publish "kv-cli". Are you logged in as the correct user?
> npm ERR! 403 In most cases, you or one of your dependencies are requesting
> npm ERR! 403 a package version that is forbidden by your security policy.

这个错误的原因是，我在 `package.json` 里使用的库名称为 `kv-cli`，已经有人将同名的库上传到 `npm` 仓库去了，因此我没有权限上传一个同名的库。

只需要将 `package.json` 里的 `name` 字段修改即可，

之后就能够成功上传库了。
