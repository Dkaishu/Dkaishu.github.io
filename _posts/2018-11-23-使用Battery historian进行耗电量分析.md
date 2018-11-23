---
layout:     post
title:      使用BatteryHistorian进行耗电量分析
subtitle:   Android 性能优化之电量优化
date:       2018-11-23
author:     dks
header-img: img/post-bg-debug.png
catalog: true
tags:
	- Android
---

> Android 性能优化之电量优化

# 使用 Battery historian 进行耗电量分析

#### 使用前
Battery historian 的安装请看前一篇[win10 搭建 Battery-Historian 运行环境](http://dkaishu.com/2018/11/23/win10%E6%90%AD%E5%BB%BABattery-Historian%E8%BF%90%E8%A1%8C%E7%8E%AF%E5%A2%83/)
感谢 [Battery historian tool 使用说明](https://wwmmyy.github.io/2016/12/14/Battery_Historian_Tool%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E/)，配图很详细。
#### 使用
1. 管理员权限运行CMD

  ```
  cd C:\Users\Administrator\go\src\github.com\google\battery-historian
  battery-historian.go
  //或直接  go run cmd/battery-historian/battery-historian.go	 
  ```

2. 访问``` http://localhost:9999```查看是否正常运行

3. 另起CMD，清空电量历史，启用唤醒锁记录，然后根据实际情况运行真机（至少3-4小时）

   ```
   adb shell dumpsys batterystats --reset
   adb shell dumpsys batterystats --enable full-wake-history
   // root 后可开启 kernel trace logging，见官网
   ```

4. 导出手机的Bugreport日志，文件位于当前 cmd 运行的目录

   ```
   // For devices 6.0 and lower:
   adb bugreport > bugreport.txt

   // Android 7.0 and higher:
   adb bugreport bugreport.zip
   ```

5. 在```http://localhost:9999/```中 Browse 并 Submit ，稍等即可看到可视化界面

   推荐查看[Battery_Historian_Tool使用说明](https://wwmmyy.github.io/2016/12/14/Battery_Historian_Tool%E4%BD%BF%E7%94%A8%E8%AF%B4%E6%98%8E/)，更详细，不再赘述。

#### 一点想法

作为开发者，可能更加关注的是自己 App 的耗电情况，并进行针对性优化，所以要倾向使用 【App Selection】、导入两个 bugreport 文件对比分析等关功能进行分析；当然找出问题是第一步，优化才是我们的目的。代码质量不可忽视。