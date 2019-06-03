---
layout:     post
title:      Android Studio 中配置阿里云公共代码库(镜像仓库)
subtitle:   Android 开发常见问题
date:       2019-06-01
author:     dks
header-img: img/post-bg-swift2.jpg
catalog: true
tags:
   - MySQL
---




Android 开发中最常见的问题之一就是 Gradle插件和三方库依赖包无法下载或下载十分缓慢，如[AndroidStudio导入项目时一直卡在 Building gradle project info 的问题](http://dkaishu.com/2018/11/27/AndroidStudio%E5%AF%BC%E5%85%A5%E9%A1%B9%E7%9B%AE%E6%97%B6%E4%B8%80%E7%9B%B4%E5%8D%A1%E5%9C%A8-Building-gradle-project-info-%E7%9A%84%E9%97%AE%E9%A2%98/)，这些由于【墙】引起的问题，也是本文要解决两个难题。虽然我们可以通过翻墙解决，但指不定什么时间就行不通了。阿里云镜像的方式是个不错的替代选择，不仅行得通，还能够大幅度提高工程构建速度。

## 如何配置
配置其实很简单，参照[ 阿里云官方配置指南](https://help.aliyun.com/document_detail/102512.html?spm=a2c40.aliyun_maven_repo.0.0.361830543mJYeQ),将项目根目录下的 build.gradle 文件相应部分替换即可：
```
buildscript {

    repositories {

        maven { url 'https://maven.aliyun.com/repository/central' }
        maven { url 'https://maven.aliyun.com/repository/google' }
        maven { url 'https://maven.aliyun.com/repository/gradle-plugin' }
        maven { url 'https://maven.aliyun.com/repository/jcenter' }
        maven { url 'https://maven.aliyun.com/repository/public' }
        
        mavenLocal()

        google()
        jcenter()

    }
    dependencies {
    		//这里版本根据自己项目而定
        classpath 'com.android.tools.build:gradle:3.2.1'
    }
}

allprojects {
    repositories {

        maven { url 'https://maven.aliyun.com/repository/central' }
        maven { url 'https://maven.aliyun.com/repository/google' }
        maven { url 'https://maven.aliyun.com/repository/gradle-plugin' }
        maven { url 'https://maven.aliyun.com/repository/jcenter' }
        maven { url 'https://maven.aliyun.com/repository/public' }
    
        mavenLocal()
        google()
        jcenter()
        maven {
            url 'https://jitpack.io'
        }
    }
}
```
由于查找相应库的顺序是从前到后，所以即使重复配置也是可行的，可以看到，我这里
```  
maven { url 'https://maven.aliyun.com/repository/google' }
maven { url 'https://maven.aliyun.com/repository/jcenter' } 
```
与 
```
google()  
jcenter() 
```
其实是重复的,防止在前者中找不到。关于这些仓库的区别，在[参考]中可找到。
配置简单，但实际操作中可能会遇到不少问题。

## 常见错误
#### 1.代理配置问题
在更换为阿里云镜像之前，我们很可能启用了FQ软件，可能Android Studio 中配置了代理，如果你的报错信息中带有类似`127.0.1`之类的文字，你首先要解决的就是取消代理设置：
1. Appearance&Behavior ->  System Setting  -> HTTP Proxy 设为No Proxy ；
2. 保证工程根目录下的 `gradle.properties` 的代理设置已经去掉；
3. 检查系统Gradle配置文件`.gradle/gradle.properties`中的代理配置已经去掉。注：mac 下 在`/Users/用户名/.gradle/gradle.properties` windows下 在`C:\Users\Administrator.gradle\gradle.properties`

#### 2.部分依赖库找不到报错 
更换后，我们可能会发现，大部分库都已经下载了，但是有少部分库依然报红色错误，这很可能是阿里云仓库中没有相应的库。解决方法，可参照上面配置，将原库加上，阿里云找不到就在原库中找。

#### 3.改后发现不奏效，无反应等
1. build -> clean project；
2. file -> invalidate caches / restart
两个万能操作，哪个好使用哪个。

## 参考
- [阿里云指南](https://help.aliyun.com/document_detail/102512.html?spm=a2c40.aliyun_maven_repo.0.0.361830543mJYeQ)
- [仓库地址及目录](https://maven.aliyun.com/mvn/view)
- [Gradle仓库目录](https://services.gradle.org/distributions/)
- [Gradle与Gradle warper 版本对照](https://developer.android.com/studio/releases/gradle-plugin.html)
- [Android Studio阿里镜像配置](https://www.jianshu.com/p/b038bd95444b)
- [`Difference among mavenCentral(), jCenter() and mavenLocal()?`
](https://stackoverflow.com/questions/50726435/difference-among-mavencentral-jcenter-and-mavenlocal/50726436)