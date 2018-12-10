---
layout:     post
title:      Gradle在Android中的应用（二）
subtitle:   认识Gradle
date:       2018-11-15
author:     dks
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
   - Android
   - Gradle
---

>认识Gradle

# Gradle在Android中的应用（二）



## 配置文件
基于 Grade 构建的项目至少有一个 build.gradle ，Android中目录结构通常是这样的：
```
 Myproject
   │
   ├── .gradle
   └── app
   │    │
   │    ├── build
   │    ├── libs
   │    ├── src
   │    │    └── main
   │    │        ├── java
   │    │        │   └── com.package.myapp
   │    │        └── res
   │    │            ├── drawable
   │    │            ├── layout
   │    │            └── etc.
   │    ├── build.gradle
   │	└── ...
   │
   ├── gradle
   │    └── wrapper
   │         ├── gradle-wrapper.jar
   │    	 └── gradle-wrapper.properties
   │    
   ├── build.gradle
   ├── gradle.properties
   │
   ├── gradlew
   ├── gradlew.bat
   │
   ├── settings.gradle
   │
   └── ...
```
Gradle 使用了一个叫做 source set 的概念，官方解释：一个 source set 就是一系列资源文件，其将会被编译和执行。对于Android项目，main 就是一个 source set，其包含了所有的资源代码。当你开始编写测试用例的时候，你一般会把代码放在一个单独的 source set，叫做 androidTest ，这个文件夹只包含测试。

#### .gradle文件夹、gradlew、gradlew.bat
- gradlew：`Gradle start up script for UNIX` Unix系列系统启动 Gradle 的脚本
- gradlew.bat：` Gradle startup script for Windows` window系统启动 Gradle 的脚本
- .gradle文件夹：运行项目时自动生成
**这三个都不需要我们去改动**，无视他们就好

#### 根目录gradle文件夹
首先我们需要明白 Gradle Wrapper 的概念：Grade 只是一个构建工具，而新版本总是在更迭，所以 Gradle Wrapper 应运而生，如同它的名字，它是对 Gradle 的包装。并且 Gradle Wrapper 提供了一个windows 的 batch 文件和其他系统的 shell 文件，即上文提到的```gradlew```和```gradlew.bat```，当我们去build 项目时，会运行这个脚本，这个时候，才去真正下载相应的Gradle版本。开发者不需要为你的电脑安装任何 gradle 版本，避免由于gradle版本更新导致的问题。
这个文件夹就相当于提供给Gradle的运行环境，就像Android需要Java环境。
```
#Tue May 15 09:47:55 CST 2018
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists
distributionUrl=https\://services.gradle.org/distributions/gradle-4.6-all.zip
```
以上是 gradle-wrapper.properties 的内容，通常除了最后一项，我们无需改动。distributionUrl 指的是我们所需下载的文件的连接，并可以直观看出其版本。要修改 Gradle 的版本，直接修改这里的版本号即可。当我们下载太慢时，可手动去下载。可参考： [AndroidStudio导入项目时一直卡在 Building gradle project info 的问题](http://dkaishu.com/2018/11/27/AndroidStudio%E5%AF%BC%E5%85%A5%E9%A1%B9%E7%9B%AE%E6%97%B6%E4%B8%80%E7%9B%B4%E5%8D%A1%E5%9C%A8-Building-gradle-project-info-%E7%9A%84%E9%97%AE%E9%A2%98/)

#### gradle.properties
此文件用于对 gradle project 的属性配置，建议的配置：
```
# 配置大内存、报告OOM错误、UTF-8编码
org.gradle.jvmargs=-Xmx2048m -XX:MaxPermSize=512m -XX:+HeapDumpOnOutOfMemoryError -Dfile.encoding=UTF-8
# 守护进程
org.gradle.daemon=true
# 并行编译
org.gradle.parallel=true
# 开启缓存
android.enableBuildCache=true
# 开启孵化模式：
org.gradle.configureondemand=true
```

#### settings.gradle
它的主要作用就是表明当前根 project 下包括的子 project 。setting.gradle 文件在初始化时期被执行

```
include ':app',':otherlib'
```
以上表示当前主 project （也就是 module ）下有 app，otherlib 两个子 project，如同在 AndroidStudio面板 Gradle标签页里看到的那样。

#### build.gradle
顶层根目录下的 build.gradle 文件的配置最终会被应用到所有项目中，每个 module 下的 build.gradle 仅作用于当前  module。
对于根 build.gradle 和每个 module 下的 build.gradle，接下来会再详细讲解。请看下一篇。