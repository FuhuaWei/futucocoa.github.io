---
layout: post
title:  "微信终端跨平台组件Mars介绍"
date:   2017-01-20 11:14:32 +0800
tags: [mars, c++]
author: deger

---

第一次听到微信终端跨平台组件 mars 要开源的消息时，我个人是比较兴奋的。富途证券同样有Android、iOS、Mac、Windows等客户端，我们也在开始做跨平台组件。微信的Mars组件，作为微信客户端的一部分，多年数以亿计的用户验证，非常值得期待。

跨平台组件好处很明显，富途证券的iOS、Mac客户端从一开始就使用同一个底层，相比其他客户端，网络层、协议层、数据层、很多逻辑代码都是可以共用，可以省去很多时间。

现在[Mars已经开源](https://github.com/Tencent/mars)，官方有丰富介绍文档，也有QQ群供交流讨论，建议大家先自己看看Wiki文档，一些明确说明了的、浅显的问题就不要问了。比如为什么Windows平台编译不通过，人家官方已经明确说Windows暂不支持，但很快会支持到。


## Mars官方说明

Mars 是微信官方的终端基础组件, 是一个业务性无关,平台性无关 使用C++ 编写的基础组件。目前已接入微信 Android、iOS、Mac、Windows、WP、UWP 等客户端。注意：目前仅支持Android、iOS、Mac 平台，其他平台会在后续的版本中很快支持

它主要包括以下几个部分：

- Comm：基础库，包括socket、线程、消息队列、协程等基础工具；
- Xlog：通用日志模块，充分考虑移动终端的特点，提供高性能、高可用、安全性、容错性的日志功能
- SDT：网络诊断模块；
- STN：信令传输网络模块，负责终端与服务器的小数据信令通道。包含了微信终端在移动网络上的大量优化经验与成果，经历了微信海量用户的考验。


![图片](https://github.com/WeMobileDev/article/raw/master/assets/mars/mars.png?raw=true)

## 模块分析

在macOS系统下载源码 ，运行python脚本编译代码

```
git clone https://github.com/Tencent/mars.git
cd mars
./libraries/build_apple.py
```

打开`mars-open-mac.xcodeproj`可以看到大概的项目结构。

- sdt-mac
- stn-mac
- log-mac
- comm-mac
- baseevent-mac
- app-mac
- openssl-mac


### 1. Xlog日志模块

据官方文档[微信终端跨平台组件 mars 系列（一） - 高性能日志模块xlog](http://mp.weixin.qq.com/s/cnhuEodJGIbdodh0IxNeXQ)，Xlog优点很多。

- Xlog支持日志分级，类似Android原生支持的日志系统和苹果平台的[CocoaLumberjack](https://github.com/CocoaLumberjack/CocoaLumberjack)库。
- Xlog支持加密，支持压缩（83.7%的压缩率）
- Xlog很好的兼顾这四点的前提下做到：高性能高压缩率、不丢失任何一行日志、避免系统卡顿和 CPU 波峰。
	- 流畅性 - 不能影响程序的性能。最基本的保证是使用了日志不会导致程序卡顿
	- 完整性 - 不能因为程序被系统杀掉，或者发生了 crash，crash 捕捉模块没有捕捉到导致部分时间点没有日志， 要保证程序整个生命周期内都有日志。
	- 容错性 - 不能因为部分数据损坏就影响了整个日志文件，应该最小化数据损坏对日志文件的影响。
	- 安全性 - 不能把用户的隐私信息打印到日志文件里，不能把日志明文打到日志文件里
- Xlog还针对移动系统、硬件做了一些特殊优化，避免了常见的坑

Xlog主要实现在`log`项目和`comm`项目的`xlogger`目录中。`log`项目实现日志内容的读写、压缩逻辑。`xlogger`实现了日志分级、日志禁用逻辑，提供用于增加日志的c++接口如`xerror2(...)`，`xinfo2_if(exp, ...)`等。`MacDemo`样例项目中的`LogUtil`实现了ObjC接口。

XLog使用方法

```
    // init xlog
#if DEBUG
    xlogger_SetLevel(kLevelDebug);
    appender_set_console_log(true);
#else
    xlogger_SetLevel(kLevelInfo);
    appender_set_console_log(false);
#endif
    appender_open(kAppednerAsync, [[libraryDirectory stringByAppendingString:@"/log/"] UTF8String], "test"); 
```

- `xlogger_SetLevel`设置日志级别
- `appender_set_console_log`设置是否在console中打印日志
- `appender_open`设置日志目录、日志文件前缀

保存的日志文件需要解压才可以查看，官方提供了python脚本`decode_mars_log_file.py`来解压。

```
python ./log/crypt/decode_mars_log_file.py ~/Library/log/test_20170120.xlog
```


日志默认是不加密的，如果更改加密算法的话，只需要修改两个函数：

```
void CryptSyncLog(const char* const _log_data, size_t _input_len, char* _output, size_t& _output_len);
void CryptAsyncLog(const char* const _log_data, size_t _input_len, char* _output, size_t& _output_len);
```

暂时没有仔细研究日志加密，不过估计日志加密后，需要修改python脚本才能读取明文了。

### 2. STN信令传输网络模块

STN是一个跨平台的socket层解决方案，支持TCP协议，并且对IP选择、连接超时、读写超时等常见问题做了针对性的优化。官方也针对这些优化写了两篇文章：

- [微信终端跨平台组件 mars 系列(二) - 信令传输超时设计](http://mp.weixin.qq.com/s/PnICVDyVuMSyvpvTrdEpSQ)
- [微信终端跨平台组件 Mars 系列（三）连接超时与IP&Port排序](http://mp.weixin.qq.com/s?__biz=MzAwNDY1ODY2OQ==&mid=2649286458&idx=1&sn=320f690faa4f97f7a49a291d4de174a9&chksm=8334c3b8b4434aae904b6d590027b100283ef175938610805dd33ca53f004bd3c56040b11fa6#rd)


STN支持HTTP，但支持得并不好。考虑到HTTP并不安全，苹果平台已经强制使用HTTPS，mars要支持HTTPS还需要时间。

### 2.1 TCP连接简单使用

TCP连接对外的接口主要在`stn.h`、`stn_logic.h`、`longlink_packer.h`中。如果简单使用，大致看看这几个头文件即可。具体使用需要设置Callback、设置IP列表、发送请求。

```
//设置Callback
void SetCallback(Callback* const callback);
    
//设置IP列表
void SetLonglinkSvrAddr(const std::string& host, const std::vector<uint16_t> ports);
    
//设置保底IP列表
void SetBackupIPs(const std::string& host, const std::vector<std::string>& iplist);
    
//如果未连接则快速连接
void MakesureLonglinkConnected();
    
//发送请求（异步）
void StartTask(const Task& task);
    
```

每个请求即一个`Task`, `Task`包含taskid, cmdid, need_auth, retry_count等属性，而task的组包、解包是通过Callback回调方法处理的。

```

//底层获取task要发送的数据
virtual bool Req2Buf(int32_t taskid, void* const user_context, AutoBuffer& outbuffer, int& error_code, const int channel_select) = 0;

//底层回包返回给上层解析
virtual int Buf2Resp(int32_t taskid, void* const user_context, const AutoBuffer& inbuffer, int& error_code, const int channel_select) = 0;

//网络层收到push消息回调
virtual void OnPush(int32_t cmdid, const AutoBuffer& msgpayload) = 0;
```

上面这些回调只是包体的组包解包，包头的处理在`longlink_packer.h`中。富途的TCP协议包头跟样例中包头不一样，需要改写这里的代码。很纳闷为什么没有用mars::stn::Callback回调的方式让使用方处理。


```
/**
 * package the request data
 * _cmdid: business identifier
 * _seq: task id
 * _raw: business send buffer
 * _packed: business send buffer + request header
 */
void longlink_pack(uint32_t _cmdid, uint32_t _seq, const void* _raw, size_t _raw_len, AutoBuffer& _packed);

/**
 * unpackage the response data
 * _packed: data received from server
 * _cmdid: business identifier
 * _seq: task id
 * _package_len:
 * _body: business receive buffer
 * return: 0 if unpackage succ
 */
int  longlink_unpack(const AutoBuffer& _packed, uint32_t& _cmdid, uint32_t& _seq, size_t& _package_len, AutoBuffer& _body);

```

### 2.2 TCP连接复杂情况

网络管理类`NetCore`是一个单列，持有短连接管理类`ShortLinkTaskManager`, 长连接（TCP连接）管理类`LongLinkTaskManager`。`LongLinkTaskManager`只管理一个TCP连接`longlink`。可见默认是只有一条TCP连接，不支持不同协议连接不同服务器的情况。富途客户端用不同的特定TCP连接分别处理交易服务和行情服务，则需要自己费一番功夫。

- 方案1:修改`NetCore`，持有两个`LongLinkTaskManager`，通过回调选取Manager
- 方案2:修改`LongLinkTaskManager`的管理类，持有两个`longlink`，通过回调选取`longlink`


### 3. SDT网络诊断模块

- 通过ping、dns、tcp、http等方式检测网络情况
- 流量统计


### 4. Comm基础库模块

包括socket、线程、消息队列、协程等基础工具，也对平台差异性做了一些抽象处理。

### 5. 其他模块

* openssl - 第三方源码，包含一些加密解密的代码
* app - 提供了接口便于上层设置账户信息（用户ID，用户昵称）、设备信息（设备名称、设备类型）、应用信息（应用版本、文件目录）等接口。
* baseevent - 一些系统事件的处理，如前后台切换、网络变化

## 几点建议

- 官方拿Mars跟[AFNetworking](https://github.com/AFNetworking/AFNetworking), [OKHttp](https://github.com/square/okhttp)做对比，其实有点奇怪。另外两个都不是跨平台的库也不涉及TCP连接，没什么可比性。在看源码之前，我认为还不如跟[Boost](http://www.boost.org)、[POCO](https://pocoproject.org)来做对比。看了源代码后，我估计官方没有拿Boost来做对比，可能是因为Mars本身都是依赖Boost的。

|  | Mars | Boost | POCO | libuv
|--------|--------|--------|--------|
|  跨平台  | yes | yes | yes | yes
|  实现语言  | C++ | C++ | C++ | C++ |
|  包含TCP实现  | yes | yes | yes | yes |
|  包含HTTP实现  | yes | no | yes | no |
|  包含日志模块  | yes | no | yes | no |
|  加密模块  | yes(openssl) | no | yes | no |
|  xml模块  | yes(tinyxml) | no | yes | no |
|  json模块  | no | no | yes | no |
|  包含数据库模块  | no | no | yes | no |
|  开源协议  | MIT | Boost | Boost | MIT |

- 对于依赖Boost库这个事实，官方这么多文档都很少提及，是看代码才发现的。
- Mars源码注释非常少，希望开源社区能持续完善
- HTTP模块跟TCP模块分离，HTTPS的实现还是交由各平台实现就好
- TCP模块能默认支持多条TCP连接
- TCP协议包头也通过mars::stn::Callback回调处理
- 增加TCP连接使用样例
- 增加日志加密解密样例


## 总结

Mars总体来说是一个非常不错的开源组件，像Xlog日志模块、TCP连接模块、SDT网络诊断模块、Comm基础库提供的工具都是可以直接借用或借鉴的。

