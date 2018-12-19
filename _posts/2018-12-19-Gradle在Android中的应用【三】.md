---
layout:     post
title:      Gradle在Android中的应用（三）
subtitle:   根目录下的 build.gradle
date:       2018-12-19
author:     dks
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
   - Android
   - Gradle
---

> 根目录下的 build.gradle

# Gradle在Android中的应用（三）

## 根目录下的 build.gradle

根目录下的 build.gradle 是对整个project的定义配置：```Top-level build file where you can add configuration options common to all sub-projects/modules.```根据下面这个示例，我们一一解读。
```
// Top-level build file where you can add configuration options common to all sub-projects/modules.
apply from: "config.gradle"

buildscript {
    repositories {
        google()
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.2.1'
        classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2'
        // NOTE: Do not place your application dependencies here; they 			// belong in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        google()
        jcenter()
        maven {
            url 'https://jitpack.io'
        }
        maven {
            url "https://repo.eclipse.org/content/repositories/paho-snapshots/"
        }
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}

 //ext {
 //      compileSdkVersion = 22
 //     buildToolsVersion = "22.0.1"
 //} 
```

#### buildscript 方法
buildscript 方法定义了全局的相关属性。其中 repositories 定义了插件依赖包的来源： google 和 jcenter 仓库。dependencies 用来定义构建过程中所使用的Android插件，如同注释所说，不要在这里定义 module 中所使用的依赖包。

#### allprojects 方法
allprojects 方法中的定义作用于各个模块，repositories 定义了module中依赖包的来源，示例中不仅定义了 google 和 jcenter 两个中心仓库，还定义了两个个人 maven 仓库，其 url 表明了它的地址。

#### task
这里默认定义了一个名为 clean 的 task ，它用于删除build文件下的文件。当我们点击AndroidStudio的Build ->clean project 时就会执行。另外值得一提的是，Gradle 中有四种基本的命令: assemble ; check ; build  ; clean 。展开 AndroidStudio 右侧的 Gradle 标签页 ，你会发现它们。当然我们也可以自己定义 task，不过 Android 开发中，我们基本不会用到。

#### ext
ext 中定义的是在全局的 gradle 文件中的属性，一般常用来定义全局统一的常量值，例如compileSdkVersion buildToolsVersion 等等。定义之后，我们就可以在 module 下的Build.gradle 中引用了：
```
android {
       compileSdkVersion rootProject.ext.compileSdkVersion
       buildToolsVersion rootProject.ext.buildToolsVersion
 }
```

#### apply from
当我们需要在 ext 中定义较多的属性时，我们可以将其独立出来，编写一个 .gradle 文件，示例中我的是 config.gradle , 通过`apply from: "config.gradle"` 将其引入，其作用等同于 ext 中定义。但是，我并不推荐这样去写，因为他会隐藏错误，例如当你写错一个单词时，Gradle构建时无法执行，报错时不会提示到你具体的错误处，因为它还没有强大到理解你自定义的内容。下面是 config.gradle 中的内容：
```
ext {
    support = '27.1.1'

    retrofit2 = '2.3.0'
    
    //其他...

//android 这个组名不要改
    android = [
            applicationId    : "com.tincher.examandmentoring",
            applicationName  : "ExamAndMentoring",
            providerName     : "com.tincher.ex.fileProvider",

            compileSdkVersion: 26,
            minSdkVersion    : 16,
            targetSdkVersion : 26,
            versionCode      : 1,
            versionName      : "1.0.0",

            versionCodeLib   : 1,
            versionNameLib   : "1.0.0",
    ]

    
    //为方便区分，可将其分成多组。嫌麻烦，放在一组中即可。分组的名字自己定义，
    //每项逗号隔开
    suptDeps = [
            suptAppcompat:"com.android.support:appcompat-v7:${support}",
            supportV4    : "com.android.support:support-v4:${support}",
            supportDesign: "com.android.support:design:${support}"
            // 其他...
       
 	]
 	
 	baseDeps = [
 	        retrofit2:"com.squareup.retrofit2:retrofit:$retrofit2"
 	        // 其他...
 	    ]
```

在 module 下的 build.gradle 引用并使用它们：

```
def mAppName = rootProject.ext.android.applicationName

android {
    compileSdkVersion rootProject.ext.android.compileSdkVersion
    defaultConfig {
        applicationId rootProject.ext.android.applicationId
        minSdkVersion rootProject.ext.android.minSdkVersion
        targetSdkVersion rootProject.ext.android.targetSdkVersion
        versionCode rootProject.ext.android.versionCode
        versionName rootProject.ext.android.versionName

		buildConfigField "String", "mAppName", "\"${mApplicationName}\""
    }

}

```

## 下一篇 module目录下的 build.gradle 
