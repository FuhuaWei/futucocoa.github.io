---
layout: post
title:  "Mac App 签名"
date:   2016-10-14 13:18:01 +0800
tags: [macOS, shell]
author: deger

---

从`macOS` `10.11`开始，默认只能运行从`App Store`下载的App。通过修改`系统设置`-`安全性与隐私`-`通用`-`允许从以下位置下载的应用`为`App Store和被认可的开发者`，可以运行第三方网站下载的已签名App。大部分未签名的App将会被视为不安全的应用，用户无法正常运行，虽然能回到系统设置中放开限制，但是大部分用户并不会这么做。

那么我们怎么知道一个App是否已签名，怎么去签名，怎么重新签名？就要用到`codesign`命令，具体使用方法可运行`man codesign`查看。下面做一个简单的介绍

## 手动签名

大部分情况下，我们都是直接用Xcode做的签名，因为太方便了。如果我们做`持续集成（Continuous integration）`，我们就可能需要用命令行做手动签名。

```
➜  codesign -s "Developer ID Application: Shenzhen Futu Network Technology Company Limited" FutuNiuniu.app
```

有些App包含了子App、动态库，需要增加参数`--deep`，即`codesign --deep -f -s`，否则会出现类似错误

```
FutuNiuniu.app: code object is not signed at all
In subcomponent: /Applications/FutuNiuniu.app/Contents/Frameworks/FTTrade.framework
```

如果出现`resource fork, finder information, or similar detritus not allowed`错误，可以参数运行`xattr -rc FutuNiuniu.app`, 相关说明看[这里](http://stackoverflow.com/questions/37830758/code-signing-error-whenever-i-try-replacing-stock-files-in-new-scenekit-applicat)


## 验证签名
我们可以从应用程序目录随便挑几个App试试，使用参数`-v`
`
➜  codesign -v FutuNiuniu.app
`
如果App已签名，什么都不会打印，如果App未签名，则会打印
`FutuNiuniu.app code object is not signed at all`

对于已签名的App，使用参数`-vvv`会有略详细的输出结果，如果使用参数`-dvvv`会有非常详细的签名情况

```
➜  codesign -dvvv FutuNiuniu.app
Executable=/Applications/FutuNiuniu.app/Contents/MacOS/FutuNiuniu
Identifier=cn.futu.Niuniu
Format=app bundle with Mach-O thin (x86_64)
CodeDirectory v=20200 size=106058 flags=0x0(none) hashes=5294+5 location=embedded
Hash type=sha1 size=20
CandidateCDHash sha1=abe6c481a86a990d118cd9a5ee3e76d20bf4abca
Hash choices=sha1
CDHash=abe6c481a86a990d118cd9a5ee3e76d20bf4abca
Signature size=8617
Authority=Developer ID Application: Shenzhen Futu Network Technology Company Limited
Authority=Developer ID Certification Authority
Authority=Apple Root CA
Timestamp=2016年8月26日 下午5:45:29
Info.plist entries=29
TeamIdentifier=6HE4ZULJ45
Sealed Resources version=2 rules=12 files=2199
Internal requirements count=1 size=208
```

假设我们修改已签名的App，比如我们修改QQ音乐`Info.plist`里面的版本号`CFBundleShortVersionString`的值，再检查签名。

```
➜  vi QQMusic.app/Contents/Info.plist 
➜  codesign -v QQMusic.app
QQMusic.app: invalid Info.plist (plist or signature have been modified)
In architecture: x86_64

➜  open QQMusic.app 
LSOpenURLsWithRole() failed with error -10810 for the file /Applications/QQMusic.app.
```

签名不合法了，此时再去打开这个App，系统会弹窗提示错误`Sandbox registration failed: The code signature is not valid: The operation couldn’t be completed. (OSStatus error -67030.)`。

## 重新签名

对于上面的修改过的QQ音乐，我们可以通过重新签名，又可以让应用运行起来。强制重新签名需要增加参数`-f`，另外要保留原来的沙盒属性，需要增加参数`--preserve-metadata`。

```
➜  codesign -f --preserve-metadata=entitlements -s "Developer ID Application: Shenzhen Futu Network Technology Company Limited" QQMusic.app 
QQMusic.app: replacing existing signature
QQMusic.app: Permission denied
➜  sudo codesign -f --preserve-metadata=entitlements -s "Developer ID Application: Shenzhen Futu Network Technology Company Limited" QQMusic.app
Password:
QQMusic.app: replacing existing signature
➜  open QQMusic.app 
```

也就是说`App Store`里的很多应用，都可以修改里面的资源文件，甚至代码，重新打包签名，并对外发布。有没有细思极恐，Mac应用跟Android一样也是可以重新打包发布的。之前出现过的[XcodeGhost风波](https://zh.wikipedia.org/wiki/XcodeGhost风波)，就有很多国内互联网大公司中招。


其实Xcode也很好的实现了重新签名的工作，如果我们要把其他人的App打包到我们的App里，直接拖入项目，在打包和提交App Store的时候，Xcode会对子App重新签名，经测试可正常运行。



说到签名，不得不赞一下Xcode的改进，极大的提高了开发效率。几年前我们还经常需要花时间去处理`Provisioning Profile`, `Certificate`，`Entitlement`等一堆问题，现在程序员基本上不需要去管这些问题了。
