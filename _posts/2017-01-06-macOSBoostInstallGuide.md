---
layout: post
title:  "macOS 中Boost的安装和使用"
date:   2017-01-06 12:42:34 +0800
tags: [macOS, boost]
author: deger

---

Boost是一个功能强大、构造精巧、跨平台、开源并且完全免费的C++程序库，有着“C++‘准’标准库”的美誉，值得每位C++程序员学习使用。

## 1 安装Boost

### 1.1 使用源码安装

1. 下载[Boost源码](http://www.boost.org)
2. 解压放在任意目录，例如`/usr/local/boost_1_63_0`
3. `./bootstrap.sh `
4. `./b2 headers`
5. `./b2`
6. 留意运行日志头文件目录 `/usr/local/boost_1_63_0`, lib目录`/usr/local/boost_1_63_0/stage/lib`

打开源码中index.html查看使用文档

### 1.2 使用Homebrew安装

1. 下载安装[HomeBrew](http://brew.sh)
2. `brew install boost`
3. 留意运行日志会显示头文件目录 `/usr/local/Cellar/boost/1.60.0_2/include`, lib目录`/usr/local/Cellar/boost/1.60.0_2/lib`


### 1.3 使用MacPort安装

1. 下载安装[MacPort](https://www.macports.org/install.php)
2. `sudo port install boost`

## 2. 在XCode项目中使用Boost

1. 新建一个Command Line Tool项目
2. 在Build Setings - Header Search Paths 增加头文件目录
3. 替换main.cpp中代码，运行！输入任意数字回车可看到结果。

```cpp
#include <iostream>
#include <boost/lambda/lambda.hpp>

int main(int argc, const char * argv[]) {
    
    printf("Please input any number:");
    using namespace boost::lambda;
    typedef std::istream_iterator<int> in;
    
    std::for_each(
                  in(std::cin), in(), std::cout << (_1 * 3) << " " );
    
    return 0;
}

```

## 3. 在XCode项目中使用Boost Lib库

Boost的很多功能都是直接在hpp头文件里实现的，比如上面的lambda例子不用导入任何lib就可以运行了。但也有一部分需要依赖指定lib库才能使用。比如下面这个正则表达式的例子:

```cpp

#include <iostream>
#include <boost/regex.hpp>

int main(int argc, const char * argv[]) {
    std::string   str = "2013-08-15";
    boost::regex  rex("(?<year>[0-9]{4}).*(?<month>[0-9]{2}).*(?<day>[0-9]{2})");
    boost::smatch res;
    
    std::string::const_iterator begin = str.begin();
    std::string::const_iterator end   = str.end();
    
    if (boost::regex_search(begin, end, res, rex))
    {
        std::cout << "Day:   " << res ["day"] << std::endl
        << "Month: " << res ["month"] << std::endl
        << "Year:  " << res ["year"] << std::endl;
    }
}
```

### 3.1 使用静态库

在Build Setings - Other linker flags `/usr/local/boost_1_63_0/stage/lib/libboost_regex.a`

如果这里直接使用lboost_regex, Xcode默认加载动态库。实际运用中可以考虑将目录中的动态库删除，只保留静态库，并在Build Setings - Library Search Paths  增加lib文件目录。

### 3.2 使用动态库

1. 在Build Setings - Library Search Paths  增加lib文件目录	
2. 将lib文件目录中的libboost_regex.dylib文件拖入项目
3. 确保在Build Phases - Link Bindary With Libraries中已经有该库
4. 在Build Phases增加Copy Files Phase, 复制libboost_regex.dylib到Frameworks


