---
layout:     post
title:      AndroidStudio导入项目时一直卡在 Building gradle project info 的问题
subtitle:   Android开发常见问题
date:       2018-11-27
author:     dks
header-img: img/post-bg-unix-linux.jpg
catalog: true
tags:
   - Android
---

>Android开发常见问题整理

# AndroidStudio导入项目时一直卡在 Building gradle project info 的问题

## 原因
问题的原因是gradle工具包（即```gradle-wrapper.properties```里对应的gradle压缩文件）本地没有需要下载，因为网络原因，下载速度及其漫长，所以会一直卡在下载的环节，读进度条。
## 最佳解决方案：
1. 在项目根目录的```build.gradle```文件中找到 gradle 版本号。如```classpath 'com.android.tools.build:gradle:3.0.1' ``` 即 3.0.1
2. 到[官网](http://services.gradle.org/distributions/)或其他站点下载对应版本的压缩包，建议下载```-all```结尾的压缩包，更全，例如我根据项目中的```3.0.1```下载```gradle-3.0-all.zip```，下载工具建议迅雷。
3. 进入```C:\Users\Administrator\.gradle\wrapper\dists```目录，找到对应文件夹，把下载的压缩包放入名字为一长串字符的文件夹内（如：```C:\Users\Administrator\.gradle\wrapper\dists\gradle-4.0.1-all\26awvqv6f41r14q9x72t4n0s```），不要解压。（部分情况下可能需要你打开```File->Settings->Build, Exectution, Deployment->Gradle ```，设置 “Gradle home” 为上述目录）
5. 重启 AndroidStudio
## 其他方法
方法一：将``` gradle-wrapper.properties```` 改为已经存在的版本。注意，大版本之间差异较大，很可能功能不支持报错。
方法二：畅通的网络环境，翻墙，并对AndroidStudio进行代理设置。
