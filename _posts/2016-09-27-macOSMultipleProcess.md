---
layout: post
title:  "macOS多进程研究"
date:   2016-09-27 16:21:50 +0800
tags: [macOS]
author: deger

---



一般来说，一个mac应用只有一个进程，为什么会想到在一个应用中使用多个进程呢？

1. 提高稳定性 - 不可否认，是程序就有崩溃多可能，将功能放到不同的进程，在某个功能出问题的情况下不影响整个应用的运行。比如safari、chrome浏览器，一个网页的卡死、崩溃，其它网页还能正常访问。
2. 方便权限控制 - 很多应用都会涉及到处理不安全的数据，比如网页、邮件、外置设备等。将不同的业务放到不同到进程，并限定进程的权限，比如网络进程只负责下载，不允许操作本地文件，即便该进程存在漏洞，也不至于影响系统。

一个应用如果由多个组织合作开发，某个功能以插件形式提供时，多进程多方式可以有效提高稳定性，也方便权限控制。

下面我们就看看如何启动多个进程，以及如何做进程间通信。

## 启动进程

### NSTask

```
NSTask *task = [NSTask new];
[task setLaunchPath:@"/usr/bin/open"];
[task setArguments:[NSArray arrayWithObjects: @"/Applications/Notes.app", nil]];
[task launch];
```
支持沙盒模式，App可以为安装包内的，也可以是其它指定目录的

### NSWorkspace

`NSWorkspace`可以通过`launchAppWithBundleIdentifier`启动可执行文件，系统会在文件系统中搜索指定`bundleIdentifier`的App来打开，如果系统有多个该`bundleIdentifier`的App，打开的App会不确定。

`NSWorkspace`也可以通过`launchApplicationAtURL`启动可执行文件，App可以为安装包内的，也可以是其它指定目录的。

```
NSError *err = nil;
NSURL *url = [[NSBundle mainBundle] URLForResource:@"Helper"
  withExtension:@"app"];
NSRunningApplication *clientRunningApp = [[NSWorkspace sharedWorkspace]
  launchApplicationAtURL:url
  options:NSWorkspaceLaunchNewInstance
  configuration:configuration
  error:&err];
```

这种方式同样支持沙盒模式，App可以为安装包内的，也可以是其它指定目录的。相比`NSTask`方式，`NSWorkspace`会返回`NSRunningApplication`指针，可以用于稍后关闭它。

```
BOOL terminated = [clientRunningApp terminate];
terminated = [clientRunningApp forceTerminate];
```

### 登录项启动

通过将子App加入到系统`登录项`，用户每次启动系统都自动打开子App进程。这种方式要求没有独立的菜单、Dock图标，可以在App的`Info.plist`中配置`LSUIElement`即`Application is agent (UIElement)`为YES。
然后用`SMLoginItemSetEnabled`方法增加删除登录项。

关于`LSUIElement`,`LSBackgroundOnly`配置可以看`NSApp`的注释说明

```
/* The following activation policies control whether and how an application may be activated.  They are determined by the Info.plist. */
typedef NS_ENUM(NSInteger, NSApplicationActivationPolicy) {
    /* The application is an ordinary app that appears in the Dock and may have a user interface.  This is the default for bundled apps, unless overridden in the Info.plist. */
    NSApplicationActivationPolicyRegular,

    /* The application does not appear in the Dock and does not have a menu bar, but it may be activated programmatically or by clicking on one of its windows.  This corresponds to LSUIElement=1 in the Info.plist. */
    NSApplicationActivationPolicyAccessory,

    /* The application does not appear in the Dock and may not create windows or be activated.  This corresponds to LSBackgroundOnly=1 in the Info.plist.  This is also the default for unbundled executables that do not have Info.plists. */
    NSApplicationActivationPolicyProhibited
};
```

苹果关于登录项说明可以看这里：[Daemons and Services Programming Guide - Adding Login Items ](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingLoginItems.html#//apple_ref/doc/uid/10000172i-SW5-BAJJBJEG)

### XPC
`XPC`可以创建没有UI界面的工作进程，可以非常方便的限定权限，支持沙盒。`XPC`有Objective-C实现的API，也有C实现的API。具体的使用方法清看这里[Daemons and Services Programming Guide -Creating XPC Services](https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPSystemStartup/Chapters/CreatingXPCServices.html)

## 进程间通信


### XPC
XPC有其独有的进程间通信方式，具体可以查看这些资料：[XPC例子](https://www.objc.io/issues/14-mac/xpc/)，[Cocoa Interprocess Communication with XPC](http://blog.yvs.eu.com/2013/07/cocoa-interprocess-communication-with-xpc/),[WWDC视频](https://developer.apple.com/videos/play/wwdc2012/241/)]

### Mach Ports

`Mach Ports`是所有进程间通信都基础，接口的文档不多，这里有[一个简单的例子](http://fdiv.net/2011/01/14/machportt-inter-process-communication)，经调试发现发送失败，错误码为`MACH_SEND_INVALID_DEST`(0x10000003 /* Bogus destination port. */)。貌似是端口有误，试了各种端口还是失败。

`Core Foundation`和`Foundation`为`Mach Ports`提供了高级API，在内核基础上封装了CFMachPort / NSMachPort / CFMessagePort，但同样没有尝试成功。
		
### Distributed Notifications

`Distributed Notifications`也有两套接口，Objective-C接口`NSDistributedNotificationCenter`跟经常使用的通知中心接口`NSNotificationCenter`类似，比较方便使用。

如果没有附带userInfo，无论是否在沙盒模式下，都是能正常接收到通知的

```
//发送端
[[NSDistributedNotificationCenter defaultCenter] postNotificationName:@"FTNiuniu" object:@"Object"];

//接收端
[[NSDistributedNotificationCenter defaultCenter] addObserver:self selector:@selector(onNotify:) name:@"FTNiuniu" object:nil];

```

但如果附带了userInfo后, 在沙盒模式下则会失败

```
[[NSDistributedNotificationCenter defaultCenter] postNotificationName:@"FTNiuniu" object:@"Object" userInfo:@{@"key":@"value"}];
//提示错误 *** attempt to post distributed notification 'FTNiuniu' thwarted by sandboxing.
```

### Distributed Objects
`Distributed Objects`的使用比较简单，服务端创建并注册一个服务`NSConnection`，指定一个服务名称`FileReader`, 指定一个服务处理实例`_reader`。

```
@protocol FileReader
- (NSString *) getFile: (NSString *)fileName;
@end

@interface FileReader : NSObject <FileReader>
@end

@implementation FileReader
- (NSString *)getFile: (NSString *)fileName
{
    return [NSString stringWithFormat:@"fileName:%@", fileName];
}

FileReader *_reader;
NSConnection *_connection;

_connection = [NSConnection defaultConnection];
_connection.rootObject = _reader;
if ([_connection registerName:@"FileReader"] == NO) {
    NSLog(@"Fail to register");
}
```

客户端根据服务名称获取到服务实例，根据协议调用方法即可

```
@protocol FileReader
- (NSString *)getFile: (NSString *)fileName;
@end

id<FileReader> _reader;
	
_reader = (id <FileReader>)[NSConnection
                               rootProxyForConnectionWithRegisteredName:
                               @"FileReader"
                               host:nil];
if (_reader == nil) {
   NSLog (@"Error: could not connect to server");
}
NSString *file = [_reader getFile:@"test"];
```

然而`Distributed Objects`并不支持沙盒，开启沙盒之后无法创建服务。

### AppleEvents & AppleScript
`AppleEvents`和`AppleScript`能很好的支持进程间通信。让一个应用支持`AppleScript`可以参考苹果官方文档[AppleScript Overview - Scriptable Applications](https://developer.apple.com/library/content/documentation/AppleScript/Conceptual/AppleScriptX/Concepts/scriptable_apps.html)

### Pasteboard
剪贴板是OS X与iOS最常见的进程间通信机制，适用于文字、图片、文档的数据交换，不能作为一般信息的传递。



## 资料

- [Designing Shared Services for the Mac App Sandbox](http://www.mattrajca.com/2016/09/12/designing-shared-services-for-the-mac-app-sandbox.html)
- [Inter-Process Communication](http://nshipster.com/inter-process-communication/)
- [进程间通信](http://lib.csdn.net/article/operatingsystem/23026)
- [Distributed Objects Programming Topics](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/DistrObjects/DistrObjects.html#//apple_ref/doc/uid/10000102-SW1)
- [GNUstep Distributed Objects](http://www.gnustep.it/nicola/Tutorials/DistributedObjects/DistributedObjects.html)
- [AppleScript Language Guide](https://developer.apple.com/library/content/documentation/AppleScript/Conceptual/AppleScriptLangGuide/conceptual/ASLR_fundamentals.html)
- [Mac Automation Scripting Guide](https://developer.apple.com/library/prerelease/content/documentation/LanguagesUtilities/Conceptual/MacAutomationScriptingGuide/index.html#//apple_ref/doc/uid/TP40016239-CH56-SW1)
