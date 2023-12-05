---
title: mac下node版本管理
date: 2022-12-17
tags: [n， node]
---

## 一、前言

在日常开发中

时常需要频繁切换 node 版本

这里记录使用工具`n`作为 node 版本管理的常用命令

<!-- more -->

## 二、常用命令

1、node 版本管理工具`n`的安装

> npm install -g n

2、将 node 升级到稳定版本

> sudo n stable

3、安装最新版本

> sudo n stable

4、安装指定版本

> sudo n 14.19.1

5、查看所有已安装的 node 版本

> n ls

6、切换 node 版本

> sudo n 14.19.1

7、卸载 node 版本

> sudo n rm 14.19.1

8、更新 npm 到最新版本

> sudo npm install npm@latest -g

9、查看当前 node 版本

> node -v
