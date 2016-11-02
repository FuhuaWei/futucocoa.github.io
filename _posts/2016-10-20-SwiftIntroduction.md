---
layout: post
title:  "Swift特性介绍"
date:   2016-10-20 15:00:34 +0800
categories: swift
author: deger

---

 - 本指引适合有经验的`Objective C`开发者。
 - 本指引并不适合Swift入门学习，只是想说说Swift的特点-简练、灵活、安全
 - [本指引代码](/assets/2016-10-20-SwiftIntroduction.playground.zip)可在Swift 3环境下运行

 Swift语言确实是吸取了各种语言的优点，很容易找到`Ruby`、`Python`、`Java`、`Lisp`的影子，相比Objective C，更现代，更优雅，更强大。
 Swift除了用来开发苹果应用，还可以用来开发网站、开发后台服务，甚至Android应用。

## 我的Swift学习路径
- 阅读官方的[The Swift Programming Language](https://itunes.apple.com/us/book/swift-programming-language/id881256329?mt=11)
- 看书的同时用`Playground`做一些简单的尝试
- 写一些demo程序
- 遇到问题随时看书或网上搜索
- 注意Swift 3跟Swift 2有挺大的差别，不要被旧的书籍、文章误导

## Swift代码简练，更接近人类语言
- 语句后面不需要分号
- 字符串用双引号即可，不需要在前面增加@
- 类型推断，编译器可以从给变量赋的值推断出改变量的类型
- 支持运算符重载
- 字符串可以用+连接，也可以在字符串中间插入值
- 整数范围集易于理解`start...end`, `start..<end`

```
import Cocoa
let company = "Futu"

func * (string: String, count: Int) -> String {
    var result = ""
    for _ in 1...count {
        result += string
    }
    return result
}

let count = 3
let result = "运算符重载结果：\(company) * \(count) = \(company * count)"
print(result)
```

## Swift非常灵活

### 枚举可以定义非纯整数值类型，还可以实现协议


```
enum Rank: Int {
    case ace = 1
    case two, three, four, five, six, seven, eight, nine, ten
    case jack, queen, king
}
var ace = Rank.ace
let aceRawValue = ace.rawValue
ace = .king

enum ServerResponse {
    case result(String)
    case failure(Int, String)
}

let successRsp = ServerResponse.result("6:00 am")
let failureRsp = ServerResponse.failure(404, "Page Not Found")
```

### `Switch`可以用于枚举、字符串、字符、元组

```
switch failureRsp {
case let .result(string):
    print("Success with result \(string).")
case let .failure(code, message):
    switch code {
    case 1...200:
        print("Network error");
    case 400...500:
        print("\(code) message:\(message)")
    default:
        print("Failure...  \(message)")
    }
}


let point = (2, 0)
switch point {
case (let x, 0):
    print("on the x-axis with an x value of \(x)")
case (0, let y):
    print("on the y-axis with a y value of \(y)")
case let (x, y) where x == y:
        print("(\(x), \(y)) is on the line x == y")
case let (x, y) where x == -y:
    print("(\(x), \(y)) is on the line x == -y")
case let (x, y):
    print("somewhere else at (\(x), \(y))")
}

```

### 闭包(Closure)，函数类型，高阶函数
下面以数组排序方法为例，数组排序方法`sort`接受类型为`(String, String) -> Bool`函数作为参数

```
let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]

//先定义方法
func sorter(s1: String, s2: String) -> Bool {
    return s1 > s2
}
names.sorted(by: sorter)

//直接使用闭包
names.sorted(by: { (s1: String, s2:String) -> Bool in
    return s1 > s2
})

//因为有类型推断，不需要写类型
names.sorted(by:{ (s1, s2) in
    return s1 > s2
})

//最后一个参数为闭包时，可写在括号后面
names.sorted { (s1, s2)  in
    return s1 > s2
}

//return可以省略, 元组括号可以省略
var s = names.sorted(by:{ s1, s2  in  s1 > s2 })
print(s)

//元组不需要命名，直接使用$0,$1表示
s = names.sorted {$0 > $1}
print(s)

//String已经实现了运算符`>`的重载，函数类型正好吻合
s = names.sorted(by:(>))
print(s)


//高阶函数再举例
names.map { (s) -> Character in
    s.characters.first!
}

//元组再举例
var a=1,b=2
(a,b) = (b,a)
a //2
b //1

```


## Swift尽可能保证安全性，除非你人为放弃

### 增加`可选值类型（Optional Value）`，显性处理`nil`的情况

类型后面有问号的才能设置成`nil`，对应oc里面的`nullable`。类型后面没有问号的属性必需在类初始化完时已经赋值。

```

var nullableInt: Int? = nil

nullableInt = 5

if nullableInt != nil {
    nullableInt!.description
    nullableInt?.description
}

if let notnullableInt = nullableInt {
    //notnullableInt 的类型是Int
    notnullableInt.description
    print(nullableInt)
    print(notnullableInt)
}

let notnullableInt = nullableInt ?? 0



class Person {
    var house: House?
//    var name: String
    
}



class House {
    var numberOfRooms = 1
}

let john = Person()
let roomCount = john.house?.numberOfRooms

```


### 严格区分`值(Value)`和`引用(Reference)`
值包括枚举和`Struct`。数据模型，`Array`，`Dictionary`用`Struct`定义，可以安全的在多线程间传递
[Value and Reference Types](https://developer.apple.com/swift/blog/?id=10)

```
let fruits = ["Apple", "Banana"]
var f2 = fruits
f2[0] = "Watermelon"
print(fruits, f2)
```

### 严格区分`常量(constant)`,`变量(variable)`
常量用`let`定义，变量用`var`定义

```
var color = #colorLiteral(red: 0.4392156899, green: 0.01176470611, blue: 0.1921568662, alpha: 1)
color = #colorLiteral(red: 0.3411764801, green: 0.6235294342, blue: 0.1686274558, alpha: 1) //change to let will fail
```

### 严格区分类型，类型之间不会隐性转化
布尔值只有`true`，`false`，不会自动将`Int`转成`Bool`

```
let int = 1
if int != 0 {
    print("true")
} else {
    print("false")
}
```

### 不用在`Switch`的`case`后面写`break`

### `if`判断后面，循环必须用花括号

### 错误处理方式更加实用


## 资料
- [Swift学习: 从Objective-C到Swift](http://www.cocoachina.com/ios/20150906/13318.html)
- [Apple Swift Blog](https://developer.apple.com/swift/blog/)
- [Learn Swift From Objective-C ](http://codewithchris.com/learn-swift-from-objective-c/)
- [Design Patterns implemented in Swift](https://github.com/ochococo/Design-Patterns-In-Swift)
- [Swift:利用Enum灵活映射多重类型Data model](http://www.open-open.com/lib/view/open1471761759386.html)
- [Swift 中 10 个震惊小伙伴的单行代码](http://www.techug.com/utm_sourcetuicoolutm_)
- [Advanced Swift中文版](http://www.jianshu.com/notebooks/2383569/latest)
- [Protocol-Oriented Programming in Swift](https://developer.apple.com/videos/play/wwdc2015/408/)