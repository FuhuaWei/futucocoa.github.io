---
layout: post
title:  "使用Swift替代复杂Bash脚本"
date:   2016-10-21 10:10:11 +0800
categories: swift
author: deger

---
# 使用Swift替代复杂Bash脚本

作为一个程序员，所有例行性、重复性工作都应该尽可能考虑用脚本去完成。比如每日编译，应用打包，代码拉取等。

在我们的项目中已经用了很多`Bash`脚本，现在我们也可以使用`Swift`语言来完成类似的工作了。

## Hello World

- 我们先来创建一个简单的`swift`脚本，创建一个`test.swift`（本文所有例子使用`Swift 3.0`）文件：
```
#!/usr/bin/swift
print("hello world")`
```
- 修改为可执行文件 
```
chmod u+x test.swfit
```
- 运行脚本：
```
./test.swift
```

### 实用技巧

#### 使用`FileManager`来操作文件
```
#!/usr/bin/swift
import Foundation

let fileManager = FileManager.default
let path = fileManager.currentDirectoryPath

print("Current Path：" + path)
```


#### 使用`CommandLine`来获取脚本参数
```
print(CommandLine.argc) // 2
print(CommandLine.arguments) // ["./test.swift", "hello"]
```
#### `Swift`运行`bash`命令
```
/***
 * 执行shell命令，返回执行结果和输出结果
 */
@discardableResult func shell(_ args: String...) -> (Int32, String) {
    let process = Process()
    process.launchPath = "/usr/bin/env"
    process.arguments = args
    
    let pipe = Pipe()
    process.standardOutput = pipe
    
    process.launch()
    process.waitUntilExit()
    
    
    let data = pipe.fileHandleForReading.readDataToEndOfFile()
    let output: String = String(data: data, encoding: .utf8)!
    
    return (process.terminationStatus, output)
}

//执行ls
var result = shell("ls").1
print("ls result:\n" + result)
```
#### 指定颜色打印
```
enum ColorCode : String {
    case black = "\u{001B}[0;30m"
    case red = "\u{001B}[0;31m"
    case green = "\u{001B}[0;32m"
    case yellow = "\u{001B}[1;33m" //粗体
    case blue = "\u{001B}[5;34m" //粗体
    case magenta = "\u{001B}[0;35m"
    case cyan = "\u{001B}[0;36m"
    case white = "\u{001B}[0;37m"
    case `default` = "\u{001B}[0;39m"

}

func + (left: ColorCode, right: String) -> String {
    return left.rawValue + right
}

func + (left: String, right: ColorCode) -> String {
    return left + right.rawValue
}

result = shell("pwd").1
print("ls result" + ColorCode.blue + "\n" + result)
```

# 相关第三方库介绍

### Rainbow
[Rainbow](https://github.com/onevcat/Rainbow)可以打印非常丰富的样式
```
public enum Color: UInt8 {
    case black = 30
    case red
    case green
    case yellow
    case blue
    case magenta
    case cyan
    case white
    case `default` = 39
    case lightBlack = 90
    case lightRed
    case lightGreen
    case lightYellow
    case lightBlue
    case lightMagenta
    case lightCyan
    case lightWhite
    
    public var value: UInt8 {
        return rawValue
    }
}


public enum Style: UInt8 {
    case `default` = 0
    case bold = 1
    case dim = 2
    case italic = 3
    case underline = 4
    case blink = 5
    case swap = 7
    
    public var value: UInt8 {
        return rawValue
    }
}

```

### SwiftShell
[SwiftShell](https://github.com/kareman/SwiftShell) 支持很多高级功能，如读取输入、异步运行命令等

# 分工实现实用的Swift脚本

| 脚本目标 | 负责人 | 预计完成时间 |
|---|---|---|
|找出项目中没有用到的图片|
|分析3套皮肤中相同图片的个数、存储大小及其占所有图片的比例|
|找出项目中未本地化的字符串|
|检查项目中注册了通知，但没有取消注册的问题|

