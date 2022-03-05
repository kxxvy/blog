---
title: GitHub Pages配置域名被占用的问题
date: 2022-03-04
tags: [GitHub]
---


今天，本打算给我的博客换个新的域名，

可在设置GitHub Pages的域名时，遇到如下提示:

<!-- more -->

> The custom domain `apiboy.cn` is already taken.
>If you are the owner of this domain, check out `https://docs.github.com/pages/configuring-a-custom-domain-for-your-github-pages-site/verifying-your-custom-domain-for-github-pages` for information about how to verify and release this domain.

看报错提示应该是域名被占用了，

可这是我刚买的域名啊，

不可能被使用过，

我百思不得其解，

在网上搜索半天也没找到问题的原因和解决办法，

最后，无奈寻求Github support的帮助。

于是我在[Ticket 1530060](https://support.github.com/ticket/personal/0/1530060)写了如下的内容:

> Today I modify the custom domain (GitHub Pages) with `apiboy.cn`
> But it warns: The custom domain `apiboy.cn` is already taken. If you are the owner of this domain, check out `https://docs.github.com/pages/configuring-a-custom-domain-for-your-github-pages-site/verifying-your-custom-domain-for-github-pages` for information about how to verify and release this domain. 
> what should I do now?

有一说一，github处理问题的速度和态度还是相当奈斯的！

大概不到一个小时，我就收到了github给我发的回复邮件：

> Thanks for writing in! The error you’re seeing generally occurs when a custom domain has previously been owned by another GitHub user, or the domain has been added to a GitHub repository in error.
> If we can verify that you own the domain, we can free it up for you.
> A quick way for us to do that would be for you to add a TXT record to your domain’s DNS records. You can follow these steps to complete the verification process:
> ...
> Once the domain has been verified, it will be removed from its current repository in 7 days. However, if you let me know once you’ve completed those steps, I can remove it immediately and save you the wait!

大体的意思就是，这个域名先前可能被某个github用户拥有过，所以才导致了这个错误，

我需要配置一条TXT记录用来证明你是这个域名的所有者，弄好了告诉他，他就可以尽快帮我搞定。

然后我按他说的照做了，几个小时后就又得到了回复：

> Thanks for verifying your domain. I have taken care of releasing the domain so that you can add it to your desired repository.

配置好域名后，就可以通过自己的新域名访问了。
