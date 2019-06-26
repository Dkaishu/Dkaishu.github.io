---
layout:     post
title:     Android Studio中Flutter代码提示卡顿卡死问题
subtitle:    初识Flutter
date:       2019-06-21
author:    dks
header-img: img/post-bg-universe.jpg
catalog: true
tags:
   - Flutter
---


使用 Android Studio 编写 Flutter 代码时，某些代码提示会卡主不动，造成假死现象，尤其是`Colors.`提示时最为明显。

## 原因
Flutter 文档提示采用 MarkDown 解析，在 Flutter 的源码文档注释中可能含有图片标签 ![]，所以在提示的时候，会访问网络，加载这些图片资源。由于墙的原因或者网速不佳的情况下，图片无法快速加载，同时 Android Studio 在加载这些资源时无法进行编辑操作，导致假死。

## 解决方法
- **方法一**： 当图片下载完以后，下次再提示时，因为有缓存所以不再卡顿。因此你可以耐心等待第一次加载，不用其他操作。
- **方法二**： AndroidStudio => Settings(Mac:Preferences) => Editor => General => Code Completion ，将`show the documentation pop in 1000ms`取消勾选，也就是将文档提示关闭。 
- **方法三**：改动Flutter源文件，将文档中的图片去除。**不建议**，因为通过Git升级Flutter时会冲突。

## 参考
- [AndroidStudio 3.3 Flutter使用Colors卡顿](https://blog.csdn.net/hxl517116279/article/details/89167219)
- [解决 Android Studio 开发 Flutter时 Colors 提示卡住的问题](https://my.oschina.net/wecnlove/blog/3025510)

