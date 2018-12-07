---
layout:     post
title:      Gradle在Android中的应用（一）
subtitle:   认识Gradle
date:       2018-11-15
author:     dks
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
   - Android
   - Gradle
---

>认识Gradle

# Gradle在Android中的应用（一）

## 什么是Gradle
```
Gradle是一个基于Apache Ant和Apache Maven概念的项目自动化建构工具。它使用一种基于Groovy的特定领域语言来声明项目设置，而不是传统的XML。

当前其支持的语言限于Java、Groovy和Scala，计划未来将支持更多的语言。

过去 Java 开发者常用 Maven 和 Ant 等工具进行封装布署的自动化，或是两者兼用，不过这两个包彼此有优缺点，如果频繁改变相依包版本，使用 Ant 相当麻烦，如果琐碎工作很多，Maven 功能不足，而且两者都使用 XML 描述，相当不利于设计 if、switch 等判断式，即使写了可读性也不佳，而 Gradle 改良了过去 Maven、Ant 带给开发者的问题，至今也成为 Android Studio 内置的封装布署工具。
```
以上是来自维基百科 [Gradle](https://zh.wikipedia.org/wiki/Gradle) 的解释。通俗的说，**Gradle 就是用来打包的一个工具，它包含了一系列的脚本，并可以自定义配置，较 Ant 和 Maven 有诸多优点**。而 Groovy 对于 Gradle，就好比 Java 对于 Android。


## 为什么Android使用Gradle来打包
Android Studio 1.0以后采用 Gradle 打包，我认为主要原因就是简单，降低开发难度。Gradle 会为我们提供很多默认的配置以及通常的默认值，而这极大的简化了我们的工作。并且新建工程时，AndroidStudio已经为我们写好了最基本的配置，甚至不用改动就能进行开发工作了。

## 理解Gradle中的几个概念
在 Grade 中的两大重要的概念，分别是 project 和 tasks。每一次构建都是有至少一个 project 来完成，所以 Android studio 中的 project 和 Gradle 中的 project 不是一个概念。

#### project
每一个 build.grade 文件代表着一个 project ，它像是 AndroidStudio 中的 Module 概念。在 Android ，它两个其实是一一对应的，每个 module 就是一个 project ，同时整个Android project 也是一个 project ，我们暂且称它为根 project 。打开 AndroidStudio 面板右侧的 Gradle 栏，你就会看到工程中的各个 project 及其父子关系。

#### tasks
每个 project 有至少一个 tasks ，tasks 在 build.gradle 中定义。一个 tasks 包含了一系列动作，然后它们将会按照顺序执行，一个动作就是一段被执行的代码，很像Java中的方法。展开 Gradle 栏中的 project ，你会看到一个 tasks 文件夹，里面就是一个个的 task，AndroidStudio3.3 版本后，运行项目时可以直观的看到每个 task 的运行情况。

#### 构建的生命周期
不包含依赖的 Tasks 总是优先执行，一旦一个tasks被执行，那么它不会再次执行了，一次构建将会经历下列三个阶段：
1. 初始化阶段：创建 project，如果有多个模块，即有多个build.gradle文件，多个project将会被创建。

2. 配置阶段：在该阶段，build.gradle 脚本将会执行，为每个 project 创建和配置所有的 tasks 。

3. 执行阶段：Gradle 会决定哪一个 tasks 会被执行，这完全依赖开始构建时传入的参数并且和当前所在的文件夹位置有关。


## 区分Gradle和Android Gradle Plugin
Gradle 是用来构建项目的，但并不是说只能用于构建 Android 的项目。

那如果我只是做 Android 开发，我也就只需要 Gradle 构建 Android 项目的功能即可，其他的又不需要，鉴于此，Gradle 封装好了基本的构建工作，然后提供了插件的接口，支持根据各自需要去扩展相应的构建任务。显然 Android Gradle 插件的开发并非是由 Gradle 官方开发的，module 下的 build.gradle 中：
```
apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'
apply plugin: 'kotlin-android-extensions'
```
表示你当前所用到的插件

根目录下的 build.gradle ：
```
buildscript {
    repositories {
        google()
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.2.1'

    }
}
```
表示你整个 project 共用的插件，这里的 3.2.1 就是当前所用的Gradle 插件的版本号，插件版本必须与Gradle 的版本相对应，以保持兼容。具体的对照表可查看[Android Gradle plugin release notes](https://developer.android.com/studio/releases/gradle-plugin#updating-gradle)。要修改 Gradle 插件版本，直接修改上面的版本号（3.2.1）即可；要修改 Gradle 的版本，直接修改根目录 `gradle/wrapper` 文件夹 `gradle-wrapper.properties`中的版本号即可。具体可参看下文 Gradle Wrapper 相关内容。当我们导入一个原有项目时，两者很有可能和你常用的版本不一致，当版本相差不大时，直接修改版本号是一个捷径。当版本差异较大时，不建议修改版本，而是要下载相应的版本，以为 Gradle 和 Gradle插件 大版本之间的差异很大，而且不兼容，会直接导致报错，项目无法运行。

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