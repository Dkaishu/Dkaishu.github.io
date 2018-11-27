---
layout:     post
title:      win10搭建BatteryHistorian运行环境
subtitle:   Android 优化之电量优化
date:       2018-11-23
author:     dks
header-img: img/post-bg-debug.png
catalog: true
tags:
   - Android
---

> Android 性能优化之电量优化

# win10 搭建 Battery-Historian 运行环境

#### 初衷

 Battery-historian 是 Google 在 GitHub 开源的电量分析工具，用它来对 Android App 进行电量消耗分析，找出元凶，解决掉，以优化性能。

#### 选择环境安装方式

Battery-historian 运行需要 go环境、Python2环境（注意：目前不支持Python3）、Java环境、git环境。所以搭建环境有两种方式选择：

1. 官方推荐的Docker环境，如果之前没用用过，推荐第二种，因为Docker环境的搭建及下载，要比第二种麻烦许多，特别是在国内网络环境下。[官网地址](https://github.com/google/battery-historian)

​      运行以下命令进行安装 Battery-historian

```
docker run -p 666:9999 gcr.io/android-battery-historian/stable:3.0
```
- 注意：win10下运行的是 ```docker run```,而不是 ```docker -- run```。
- 注意：Choose a port number and replace `<port>` with that number in the commands below.意思是说由你来设定端口号，这里是666，默认9999。不要直接复制官网的命令而写``` -p <port>:9999 ```。

2. 分别安装Go，Python2、Java、Git环境。到官网直接下载安装包运行即可，一路next，甚至连环境变量都不用配置。可参考[Battery-Historian在win10上的详细环境搭建过程](https://blog.csdn.net/sinat_34937826/article/details/79909185)

#### 运行

当以上环境安装完成并测试可用时，参照官网：

打开cmd执行，将项目下载到本地（在 go 目录下）

```
go get -d -u github.com/google/battery-historian/...
```

cd 进入go目录 。

```
cd C:\Users\Administrator\go\src\github.com\google\battery-historian
```
加载各种库等

```
go run setup.go
```

运行 Battery-historian

```
go run cmd/battery-historian/battery-historian.go
```

访问```http://localhost:9999/```

#### 可能会遇到的问题

- 注意：要以管理员权限运行 cmd，否则会报权限错误。

- 由于网络原因可能导致项目文件下载不下来，可直接下载zip，解压放到对应目录即可。

- 执行```go run setup.go```,遇到以下错误，原因是 ```battery-historian/third_party/```下```closure-library```三方库没有下载下来，到https://github.com/google/closure-library下载并放入即可

 ```
  Couldn't generate runfile: failed to run command "python C:\\Users\\Administrator\\go\\src\\github.com\\google\\battery-historian/third_party/closure-library/closure/bin/build/depswriter.py --root=C:\\Users\\Administrator\\go\\src\\github.com\\google\\battery-historian/third_party/closure-library/closure/goog --root_with_prefix=js ../../../../js":
    exit status 2
    python: can't open file 'C:\Users\Administrator\go\src\github.com\google\battery-historian/third_party/closure-library/closure/bin/build/depswriter.py': [Errno 2] No such file or directory
 ```

- 需要安装Python2 （我安装的是2.7），而不是Python3 ，不支持。