---
layout: post
title:  "macOS 10.9 子App无法正常启动的问题"
date:   2016-10-30 15:21:30 +0800
tags: [macOS]
author: deger

---

在新版即将外灰之际，测试同学发现我们的App在某些10.9版本电脑上，子进程App无法正常打开。日志显示

```
Not allowing process 5878 to register app "/Volumes/QQ 3/QQ.app/Contents/Frameworks/FTMiniNN.framework/Versions/A/PlugIns/QQ Stock Plugin.app" for launch.
16-11-29 下午9:04:31.417 QQ[5878]: Stock|FTMiniNN.m:158| 启动小牛失败:Error Domain=NSCocoaErrorDomain Code=3584 "未能开启应用程序“QQ Stock Plugin.app”，因为它已损坏。" UserInfo=0x7fb6452aa340 {NSURL=file:///Volumes/QQ%203/QQ.app/Contents/Frameworks/FTMiniNN.framework/Plugins/QQ%20Stock%20Plugin.app/, NSLocalizedDescription=未能开启应用程序“QQ Stock Plugin.app”，因为它已损坏。, NSUnderlyingError=0x7fb6452a00b0 "未能完成该操作。（OSStatus 错误 -10827。）"}
```

在查阅[StackOverFlow](http://stackoverflow.com/a/20507650)和[苹果官方文档发现](https://developer.apple.com/library/mac/documentation/security/conceptual/AppSandboxDesignGuide/AppSandboxInDepth/AppSandboxInDepth.html#//apple_ref/doc/uid/TP40011183-CH3-SW29)后发现：

对于启用了沙盒的App，如果要开启子进程App，必须满足以下几个条件任何一个：

- 主App和子App都是App Store证书签名。即从App Store下载的App将不会有这个问题
- 把主App拷贝到应用程序(/Applications)目录再运行
- 或者这个子App已经手动双击运行过。


总结下来就是，App Store下载的不会有问题，放到应用程序目录运行不会有问题，手动打开过股票插件的没问题。也就是说大部分情况都没有问题，对于有问题的小部分情况，建议弹一个提示，打开子App所在的目录，让用户主动双击打开一次即可。



