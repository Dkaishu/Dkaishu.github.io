---
layout:     post
title:     Running "flutter packages get" in flutter_app... 的问题
subtitle:    初识Flutter
date:       2019-06-21
author:    dks
header-img: img/post-bg-universe.jpg
catalog: true
tags:
   - Flutter
---



## 问题
当我们改动了`pubspec.yaml`添加依赖包时，编辑器会提示`Packages get`等按钮，当点击` Packages get` 却发现一直卡在`Running "flutter packages get" in flutter_app...`，显然，这是墙在搞事情，导致我们无法访问 Pub 服务，如同[AndroidStudio导入项目时一直卡在 Building gradle project info 的问题](http://dkaishu.com/2018/11/27/AndroidStudio%E5%AF%BC%E5%85%A5%E9%A1%B9%E7%9B%AE%E6%97%B6%E4%B8%80%E7%9B%B4%E5%8D%A1%E5%9C%A8-Building-gradle-project-info-%E7%9A%84%E9%97%AE%E9%A2%98/)一样。

## 解决办法
最简单的办法就是使用镜像。
1. 打开命令行终端，执行以下命令，设置代理：
	Mac :
	```
	export PUB_HOSTED_URL=https://pub.flutter-io.cn
	export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
	```

	Windows:
	```
	set PUB_HOSTED_URL=https://pub.flutter-io.cn
	set FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
	```
2. 进入项目根目录，执行 Packages get 命令
	```
	cd 项目根目录(`pubspec.yaml`所在目录)
	flutter packages get 
	```
	执行后出现类似下面提示，说明执行成功
	```
	Waiting for another flutter command to release the startup lock...
	Running "flutter packages get" in flutter_app...     5.5s
	```

**注意** 

- 由于我们在命令行终端进行的操作，而不是在IDE中，因此打开`pubspec.yaml`时依然会有提示，不过我们已经可以使用添加的依赖包了。
- 上述代理地址并非永久有效，可以参考<https://flutter.io/community/china> 以获得有关镜像服务器的最新动态。
