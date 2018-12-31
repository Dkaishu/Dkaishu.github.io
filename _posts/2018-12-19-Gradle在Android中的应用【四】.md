---
layout:     post
title:      Gradle在Android中的应用（四）
subtitle:   module目录下的 build.gradle
date:       2018-12-20
author:     dks
header-img: img/post-bg-e2e-ux.jpg
catalog: true
tags:
   - Android
   - Gradle

---

> module目录下的 build.gradle 【未完成，待续】

# Gradle在Android中的应用（四）

## module目录下的 build.gradle

这部分是我们最常用的部分，也是我们必须熟练的部分。首先给出最佳配置的示例，然后一一讲解：
```
apply plugin: 'com.android.application'
apply plugin: 'kotlin-android'
apply plugin: 'kotlin-android-extensions'

buildscript {
    repositories {
        google()
        jcenter()
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:${rootProject.ext.kotlin}"
        classpath "org.jetbrains.kotlin:kotlin-android-extensions:${rootProject.ext.kotlin}"
    }
}

dependencies {
	// lib 目录下所有的 .jar
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    //其他作为 library 的 module
    implementation project(':tcraftlib')
    implementation project(':viewlib')
    
    api rootProject.ext.suptDeps.supportAppcompat
    api rootProject.ext.suptDeps.supportV4
    
    debugApi rootProject.ext.baseDependencies.debugChuck
    releaseApi rootProject.ext.baseDependencies.releaseChuck


}

def mAppName = rootProject.ext.android.applicationName

android {
    compileSdkVersion rootProject.ext.android.compileSdkVersion
    defaultConfig {
        applicationId rootProject.ext.android.applicationId
        minSdkVersion rootProject.ext.android.minSdkVersion
        targetSdkVersion rootProject.ext.android.targetSdkVersion
        versionCode rootProject.ext.android.versionCode
        versionName rootProject.ext.android.versionName

        ndk { abiFilters "armeabi", "x86" }

        buildConfigField "String", "mAppName", "\"${mAppName}\""

    }

    signingConfigs {
        release {

            // v1、v2 全选即可
            v1SigningEnabled true
            v2SigningEnabled true

            // gradle.properties 中统一配置签名文件和密码
            try {
                //签名证书文件
                storeFile file(STORE_FILE)

                // 签名证书密码
                storePassword STORE_PASSWORD

                // 别名
                keyAlias KEY_ALIAS

                // 别名密码
                keyPassword KEY_PASSWORD
            }
            catch (ex) {
                throw new InvalidUserDataException("请在 gradle.properties 配置签名文件\n" + ex.message)
            }
        }

        debug {
            v1SigningEnabled true
            v2SigningEnabled true
        }
    }



    buildTypes {
        release {
            /**
             * 开启混淆
             */
            minifyEnabled true
            /**
             * 开启Zipalign优化
             */
            zipAlignEnabled true

            /**
             * 移除无用的resource文件，此项只有在开启混淆时才生效
             */
            shrinkResources true
            /**
             * 使用release证书签名
             */
            signingConfig signingConfigs.release

            /**
             * 设置混淆配置文件
             */
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'

            /**
             * 重命名apk文件，命名方式：名称+渠道名+版本号+版本名称+打包时间
             */
            applicationVariants.all { variant ->
                variant.outputs.all { output ->
                    outputFileName = "${mAppName}_${variant.name}_${variant.versionCode}_${variant.versionName}_${releaseTime()}.apk"
                }
            }
        }
        debug {
            zipAlignEnabled false
            shrinkResources false
            minifyEnabled false
            signingConfig signingConfigs.debug
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            applicationVariants.all { variant ->
                variant.outputs.all { output ->
                    outputFileName = "${mAppName}_${variant.name}_${variant.versionCode}_${variant.versionName}_${releaseTime()}.apk"
                }
            }
        }
    }


    packagingOptions {
        exclude 'proguard-project.txt'
        exclude 'project.properties'
        exclude 'META-INF/LICENSE.txt'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/NOTICE.txt'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/DEPENDENCIES.txt'
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LGPL2.1.txt'
        exclude 'META-INF/LGPL2.1'
        exclude 'META-INF/license.txt'
        exclude 'META-INF/notice.txt'
        exclude 'META-INF/ASL2.0'
    }
}

/**
 * 当前时间
 */
static def releaseTime() {
    return new Date().format("yyyyMMdd_HHmmss", TimeZone.getTimeZone("GMT+8:00"))
}


```

## apply plugin
在当前 module 中应用插件，需要放在第一行，示例中的三个插件都是 Android 官方开发，使用时按照官方使用文档配置添加即可，我们无需无法改动。其他插件同理。

## buildscript
buildscript 使用方式等同于根目录下的buildscript，既然在根目录下的 build.gradle 中我们定义了全局的 buildscript ，通常我们无需再次定义，这里再次定义主要是为了仅在此 module 中添加使用 kotlin 插件。

## dependencies
定义当前 module 使用的依赖，这也是 Gradle 的核心，仅需一行代码，即可导入依赖。可依赖 jar、aar、远程依赖、本地 library module ，（ so 文件的依赖通常放在 jniLibs 目录），通常依赖库的教程里都会这样写：
```
  dependencies {
          compile 'com.github.Dkaishu:ZXingLib:V1.0.6'
  }
```
我们这里写的没有具体的版本号，
```
		api rootProject.ext.suptDeps.supportAppcompat
```
这是因为我们在根目录下定义了全局的版本号常量，请参看上一篇。这样做的好处是方便所有的module 配置统一的 minSdkVersion、targetSdkVersion、相同版本的依赖库尤其是 support 相关库，避免各种兼容问题。

**注意**
- 依赖库的版本最好是固定的，不要` compile 'com.google.code.gson:gson:2.+'`这种写法，因为不写死的话，一方面，每次 build 时会请求网络进行检查，拖慢编译速度；另一方面会导致代码不固定，库更新后可能引起Bug或兼容问题。
- 从gradle插件3.0开始，关键字 compile、apk、provided 已经弃用，取而代之的是 implementation 和 api，implementation 的含义是指定本 module 编译时的依赖项，等同于原先的compile，它的作用范围仅限于本module ，当本 module 作为 library 时，指定的依赖项在其他依赖此 module 的 其他module 中不可用。当 用 api 的方式 指定时，其他 module 可用，其引用结构是树形结构。另外，关键字可与debug、release以及productFlavors 相结合。下面详细说明。

## 关键字implementation和api
Android 插件 3.0 以后， 有了 implementation 、api 、 compileOnly 、 runtimeOnly 这些新依赖项配置。compile 、provided、apk 虽然目前仍然可用 但已经标记为弃用，可能在4.0中消失不可用。

- implementation：依赖项在编译时对模块可用，并且**仅在运行时**对模块的依赖者可用。由于主项目编译时不再重新编译，因此可以显著缩短构建时间，**大多数应用和测试模块都应使用此配置**。
- api：依赖项在编译时对模块可用，并且在**编译时和运行时**还对模块的依赖者可用。一般情况下，**仅在库模块中使用它**。
- compileOnly：依赖项仅在编译时对模块可用，并且在编译或运行时对其依赖者不可用。 此配置的行为类似于 provided 。除非特殊情况，一般不使用。
- runtimeOnly：依赖项仅在运行时对模块及其消费者可用。 此配置的行为类似于 apk 。除非特殊情况，一般不使用。

依赖项可针对debug、release 和 productFlavors 等进行配置，互不影响，可减少代码冗余。依赖方式关键字前面加上渠道名或版本名并将Implementation等其首字母大写即可
- debug 和 release：
```
debugImplementation rootProject.ext.suptDeps.suptDebugLeakCanary
releaseImplementation rootProject.ext.suptDeps.suptReleaseLeakCanary
```
- 不同 productFlavors ：
```
//仅在 producA 渠道包 依赖 ConstraintLayout 库：
producAImplementation rootProject.ext.suptDeps.suptConstraintLayout

//二者组合：
producAReleaseImplementation rootProject.ext.suptDeps.suptConstraintLayout
```

####  多渠道打包
- productFlavors用于生成不同渠道的包，开发和编译时注意在AS左下角 Build variant 标签展开进行选择
- 可使用AS进行打包，Generate Sigined APK --> 输入签名文件及密码 --> 选择 Build Type 和 Flavor --> finsh
- 也可在 terminal 中进行：
```
执行./gradlew assembleRelease ，将会打出所有渠道的 release 包；
执行./gradlew assembleareaB ，将会打出 areaB 的 release 和 debug 版的包；
执行./gradlew assembleareaBRelease 将生成 areaB的release包。
因此，可以结合 buildType 和 productFlavor 生成不同的 Build Variants，即类型与渠道不同的组合
```
- gradle 3.0 增加了 flavorDimensions，启用3.0时需要定义该属性。升级3.0报错问题可查阅官方文档:[迁移到 Android Plugin for Gradle 3.0.0](https://developer.android.com/studio/build/gradle-plugin-3-0-0-migration.html#flavor_dimensions)

```
    productFlavors {
        /**
          * 这些渠道包的命名和配置仅作为样例，具体可根据项目更改
          */
         base {
             flavorDimensions "base"

             /**
              * 当前版本的 git sha 即 commit id，如果不是针对特定版本，一般不需要。
              */
             buildConfigField "String", "GITSHA", "\"${gitSha()}\""

             /**
              * 这里定义本渠道包的不同于 defaultConfig 的配置，
              */
             applicationId "com.tincher.androidfastdev.base"
             minSdkVersion rootProject.ext.android.minSdkVersion
             targetSdkVersion rootProject.ext.android.targetSdkVersion
             versionCode rootProject.ext.android.versionCode_areaA
             versionName rootProject.ext.android.versionName_areaA

             multiDexEnabled false
         }

         areaA {
             flavorDimensions "Areas"

         }

         areaB {
             flavorDimensions "Areas"

         }

         /**
          * 注意：名字是纯数字时，需下划线开头或加上双引号，"360"{}
          */
         _360 {
             flavorDimensions "Number"
         }
    }
```

## packagingOptions
引用的三方库会带有一些配置文件如 xxxx.properties,或者license信息，打包的时候去掉这些信息
```
    packagingOptions {
        exclude 'proguard-project.txt'
        exclude 'project.properties'
        exclude 'META-INF/LICENSE.txt'
        exclude 'META-INF/LICENSE'
        exclude 'META-INF/NOTICE.txt'
        exclude 'META-INF/NOTICE'
        exclude 'META-INF/DEPENDENCIES.txt'
        exclude 'META-INF/DEPENDENCIES'
        exclude 'META-INF/LGPL2.1'
    }
```
## 针对.so文件,配置ndk
用c或者c++写的library会被叫做so包，Android插件默认情况下支持native包，.so文件放在app包下的jniLibs包对应的文件夹中。armeabi；armeabi-v7a；mips；x86；这样不同架构的CPU会使用对应的so包，以提高性能。
```
   Android系统支持以七种不同的CPU架构，通常来说只需要考虑以下三种
   armeabi，armeabi-v7a，x86
   由于其他均兼容armeabi，且按照但要么全部支持，要么都不支持的原则，多数情况下一刀切只保留armeabi
   出于性能考虑且对安装包大小影响较小时，建议保留"armeabi", "x86"
   当"x86"的.so文件有的有，有的没有时，可能发生crash，此时仅保留 "armeabi" 即可，这是一个踩过的不易查找的坑
```
建议：
```
   ndk { abiFilters "armeabi", "x86" }
```



## 下一篇
