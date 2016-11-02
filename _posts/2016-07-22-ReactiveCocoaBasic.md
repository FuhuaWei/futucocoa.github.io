---
layout: post
title:  "ReactiveCocoa基础"
date:   2016-07-22 21:25:50 +0800
tags: [macOS, ObjC]
author: deger

---


首先来了解一下函数式反应型编程Functional Reactive Programming

函数式反应型编程是两个声明式编程的子范例(`函数式`+`反应式`)的组合


### 函数式编程

####  `编程范式(Programming paradigm)`分类
 其实就是计算机编程所使用的方法，是设计程序结构所采用的设计风格。

目前主流的编程范式有：

- `命令式编程(Imperative programming)`
	- 如Pascal，C语言
	- First DO THIS and next DO THAT	
	- 其他统称为`声明式编程(Declarative Programming)`
- `函数式编程(Functional programming)`
 	- 如Haskell，Erlang， Lisp
 	- Evaluate an expression and use the resulting value for something
- `面向对象编程(Object-oriented programming)`
 	- 如Java，C++
 	- Send messages between objects to simulate the temporal evolution of a set of real world phenomena
- `逻辑编程(Logic Programming)` 
 	- 如Prolog，Mercury，Logtalk
 	- Answer a question via search for a solution

!
[Resize icon](https://upload.wikimedia.org/wikipedia/commons/f/f7/Programming_paradigms.svg "编程范式分类")


#### 函数式编程特点
外链参考:[函数式编程初探](http://www.ruanyifeng.com/blog/2012/04/functional_programming.html)

- 函数是"第一等公民"。指的是函数与其他数据类型一样，处于平等地位，可以赋值给其他变量，也可以作为参数，传入另一个函数，或者作为别的函数的返回值。
- 只用"表达式"（expression），不用"语句"（statement）。函数式编程要求，只使用表达式，不使用语句。也就是说，每一步都是单纯的运算，而且都有返回值。
- 没有"副作用"（side effect）。意味着函数要保持独立，所有功能就是返回一个新的值，没有其他行为，尤其是不得修改外部变量的值。
- 不修改状态
- 引用透明（Referential transparency）。指的是函数的运行不依赖于外部变量或"状态"，只依赖于输入的参数，任何时候只要参数相同，引用函数所得到的返回值总是相同的。

#### 高阶函数
函数式编程的一个关键的概念是"高阶函数"。从维基百科的解释来看，一个高阶函数需要满足下面两个条件:

- 一个或者多个函数作为输入。
- 有且仅有一个函数输出。

在Objective-c中我们经常使用block作为函数。我们不需要跋山涉水地去寻找‘高阶函数’，实际上，Apple为我们提供的Foundation库中就有。考虑象下面这么简单的一个NSNumber 的数组：
	
	NSArray * array = @[ @(1), @(2), @(3) ];
	
我们想要枚举这个数组的内容，可以用一个NSArray的高阶函数来实现:

	[array enumerateObjectsUsingBlock:^(NSNumber *number, NSUInteger idx, BOOL *stop)
	{
    	NSLog(@"%@",number);
	}];

#### 函数式编程意义
- 代码简洁，开发快速
- 接近自然语言，易于理解
- 更方便的代码管理
- 易于"并发编程"


#### 函数式编程和递归
外链参考:[函数式编程扫盲篇](http://www.cnblogs.com/kym/archive/2011/03/07/1976519.html

递归是函数式编程的一个重要的概念，循环可以没有，但是递归对于函数式编程却是不可或缺的。

循环是在描述我们该如何地去解决问题。

递归是在描述这个问题的定义。

考虑经典的斐波那契数列问题1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, …，我们很容易从数列本身的定义得到一个递推式：`f(n)=f(n-1)+f(n-2)`：
        
先看循环模型：

	def Fib(n): 
    	a=1 
    	b=1 
    	n = n - 1 
    	while n>0: 
        	temp=a 
        	a=a+b 
        	b=temp 
        	n = n-1 
    	return b    	

递归模型：

	def Fib(a): 
    	if a==0 or a==1: 
        	return 1 
    	else: 
        	return Fib(a-2)+Fib(a-1)

尾递归模型：

	def Fib(a,b,n): 
    	if n==0: 
        	return b 
    	else: 
        	return Fib(b,a+b,n-1)
        	
模拟
	
         a, b, n	
	Fib (1, 1, 10)
	Fib (1, 2, 9)
	Fib (2, 3, 8)

什么是`尾递归`，用最通俗的话说：就是在最后一部单纯地去调用递归函数，这里我们要注意“单纯”这个字眼。

那么我们说下尾递归的原理，其实尾递归就是不要保持当前递归函数的状态，而把需要保持的东西全部用参数给传到下一个函数里，这样就可以自动清空本次调用的栈空间。这样的话，占用的栈空间就是常数阶的了。

在看尾递归代码之前，我们还是先来明确一下递归的分类，我们将递归分成“树形递归”和“尾递归”，什么是树形递归，就是把计算过程逐一展开，最后形成的是一棵树状的结构，比如之前的斐波那契数列的递归解法。


### 响应式编程

看下面一段代码

	a = 2
	b = 2
	c = a + b // c 是 4
	b = 3     // 现在c是多少?
	
	
在命令式编程语言中，`c = a + b`这个语句一旦执行完毕，a 和 b 再发生变化就和 c 无关了，c 并不会跟着变化。如果需要 c 变化时，我们一般会封装一个类似 update_c() 这样的函数，在 a 或 b 变化的时候调用一下，来更新c。

而在响应式编程的思想中，上面的语句实际上是建立了 c 和 a、b 的关联关系，这样，当 a 或 b 发生变化的时候，c 可以自动变化。

Excel就是响应式编程的一个例子。单元格可以包含字面值或类似”=B1+C1″的公式，而包含公式的单元格的值会依据其他单元格的值的变化而变化 。

注意这只是一个思想，或者说是目标，`我们可以使用个各种语言来实现这个目标，并不是说必须要有一种专门的响应型编程语言`。当然，语言可以根据这个思路来设计，会让响应型编程的实现更为简单。

`kvo`, `Cocoa Binding`, 



## ReactiveCocoa

外链: [ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa)  [iOS的函数响应型编程](http://udn.yyuap.com/doc/FunctionalReactiveProgrammingOniOS/chapter1/acknowledagements.html)   [ReactiveCocoa for a better world](https://github.com/blog/1107-reactivecocoa-for-a-better-world) [ReactiveCocoa v2.5 源码解析之架构总览](http://blog.leichunfeng.com/posts/2/)

ReactiveCocoa是函数式响应型编程的一个实现。它受 Functional Reactive Programming 的启发，是 Justin Spahr-Summers 和 Josh Abernathy 在开发 GitHub for Mac 过程中的一个副产品，它提供了一系列用来组合和转换值流的 API 。

ReactiveCocoa 的版本演进历程，简单介绍如下：

- <= v2.5 ：Objective-C ；
- v3.x ：Swift 1.2 ；
- v4.x ：Swift 2.x 。

本文所介绍的均为 ReactiveCocoa v2.5 版本中的内容，这是 Objective-C 最新的稳定版本。

ReactiveCocoa类图如下图所示

!
[Resize icon](http://blog.leichunfeng.com/images/ReactiveCocoa%20v2.5.png "ReactiveCocoa类图")

ReactiveCocoa 主要由以下四大核心组件构成：

- 流：`RACStream` 及其子类；
- 订阅者：`RACSubscriber` 的实现类及其子类；
- 调度器：`RACScheduler` 及其子类；
- 清洁工：`RACDisposable` 及其子类。




### 流`RACStream`
在ReactiveCocoa中，流`RACStream`代表的是随着时间而改变的值流(Streams of values over time)。
你可以把它想象成水龙头中的水，当你打开水龙头时，水源源不断地流出来；你也可以把它想象成电，当你插上插头时，电静静地充到你的手机上；你还可以把它想象成运送玻璃珠的管道，当你打开阀门时，珠子一个接一个地到达。这里的水、电、玻璃珠就是我们所需要的值，而打开水龙头、插上插头、打开阀门就是订阅它们的过程。

RACStream 是一个抽象类，通常情况下，我们并不会去实例化它，而是直接使用它的两个子类信号`RACSignal`和序列`RACSequence`。




### 信号`RACSignal`

`RACSignal`代表的是未来将会被传送的值，它是一种`push-driven`的流。

信号又是最核心的部分，其他组件都是围绕它运作的。

对于一个应用来说，绝大部分的时间都是在等待某些事件的发生或响应某些状态的变化，比如用户的触摸事件、应用进入后台、网络请求成功刷新界面等等，而维护这些状态的变化，常常会使代码变得非常复杂，难以扩展。而 `ReactiveCocoa` 给出了一种非常好的解决方案，它使用信号来代表这些异步事件，提供了一种统一的方式来处理所有异步的行为，包括代理方法、`block` 回调、`target-action 机制`、通知、`KVO` 等：

	// 代理方法
	[[self
    	rac_signalForSelector:@selector(webViewDidStartLoad:)
    	fromProtocol:@protocol(UIWebViewDelegate)]
    	subscribeNext:^(id x) {
        	// 实现 webViewDidStartLoad: 代理方法
    }];

	// target-action
	[[self.avatarButton
    	rac_signalForControlEvents:UIControlEventTouchUpInside]
    	subscribeNext:^(UIButton *avatarButton) {
        	// avatarButton 被点击了
    }];

	// 通知
	[[[NSNotificationCenter defaultCenter]
    	rac_addObserverForName:kReachabilityChangedNotification object:nil]
    	subscribeNext:^(NSNotification *notification) {
       	 	// 收到 kReachabilityChangedNotification 通知
    }];

	// KVO
	[RACObserve(self, username) subscribeNext:^(NSString *username) {
    	// 用户名发生了变化
	}];

然而，这些还只是 ReactiveCocoa 的冰山一角，它真正强大的地方在于我们可以对这些不同的信号进行任意地组合和链式操作，从最原始的输入 input 开始直至得到最终的输出 output 为止：

	[[[RACSignal
    	combineLatest:@[ RACObserve(self, username), RACObserve(self, password) ]
    	reduce:^(NSString *username, NSString *password) {
      	return @(username.length > 0 && password.length > 0);
    	}]
    	distinctUntilChanged]
    	subscribeNext:^(NSNumber *valid) {
        	if (valid.boolValue) {
            	// 用户名和密码合法，登录按钮可用
        	} else {
            	// 用户名或密码不合法，登录按钮不可用
        	}
    }];

因此，对于 ReactiveCocoa 来说，我们可以毫不夸张地说，阻碍它发挥的瓶颈就只剩下你的想象力了。


`RACSignal`可以向订阅者发送三种不同类型的事件：

- `next`：`RACSignal`通过`next`事件向订阅者传送新的值，并且这个值可以为 nil ；
- `error` ：`RACSignal`通过`error`事件向订阅者表明信号在正常结束前发生了错误；
- `completed` ：`RACSignal`通过`completed`事件向订阅者表明信号已经正常结束，不会再有后续的值传送给订阅者。
	
注意，ReactiveCocoa 中的值流只包含正常的值，即通过`next`事件传送的值，并不包括`error`和`completed`事件，它们需要被特殊处理。通常情况下，一个信号的生命周期是由任意个`next`事件和一个`error`事件或一个`completed`事件组成的。

从前面的类图中，我们可以看出，`RACSignal`并非只有一个类，事实上，它的一系列功能是通过类簇来实现的。除去我们将在下节介绍的 `RACSubject`及其子类外，`RACSignal`还有五个用来实现不同功能的私有子类：

- `RACEmptySignal`：空信号，用来实现 `RACSignal`的`+empty`方法；
- `RACReturnSignal`：一元信号，用来实现 `RACSignal` 的`+return:`方法；
- `RACDynamicSignal`：动态信号，使用一个`block`来实现订阅行为，我们在使用`RACSignal`的`+createSignal:`方法时创建的就是该类的实例；
- `RACErrorSignal`：错误信号，用来实现`RACSignal`的`+error:`方法；
- `RACChannelTerminal`：通道终端，代表`RACChannel`的一个终端，用来实现双向绑定。

对于`RACSignal`类簇来说，最核心的方法莫过于`-subscribe:`了，这个方法封装了订阅者对信号源的一次订阅过程，它是订阅者与信号源产生联系的唯一入口。因此，对于`RACSignal`的所有子类来说，这个方法的实现逻辑就代表了该子类的具体订阅行为，是区分不同子类的关键所在。同时，这也是为什么`RACSignal`中的`-subscribe:`方法是一个抽象方法，并且必须要让子类实现的原因：

	- (RACDisposable *)subscribe:(id<RACSubscriber>)subscriber {
  		NSCAssert(NO, @"This method must be overridden by subclasses");
  		return nil;
	}


### 序列`RACSequence`

`RACSequence`代表的是一个不可变的值的序列，与`RACSignal`不同，它是`pull-driven`类型的流。从严格意义上讲，`RACSequence`并不能算作是信号源，因为它并不能像`RACSignal`那样，可以被订阅者订阅，但是它与`RACSignal`之间可以非常方便地进行转换。序列提供了Foundation没有的一些高阶函数如`map`, `filter`, `fold`等。

使用`rac_sequeuece`我们能够轻松地将数组转化为一个序列:

	NSArray *array = @[ @1, @2, @3 ];
	RACSequence * stream = [array rac_sequence];

我们可以将流应用在平方数映射上，然后转化回一个数组：

	[stream map:^id (id value){
    	return @(pow([value integerValue], 2));
	}];
	NSLog(@"%@",[stream array]);

当然，我们可以合并上面的方法调用来避免污染变量的作用域.

	NSLog(@"%@",[[[array rac_sequence] map:^id (id value){
                    return @(pow([value integerValue], 2));
                }] array]);
我们来看一下`filtering`。为了使用ReactiveCocoa来过滤我们的数组，我们需要再一次把它序列化以便于使用过滤。

	NSLog(@"%@", [[[array rac_sequence] filter:^BOOL (id value){
                        return [value integerValue] % 2 == 0;
                    }] array]);
最后看一下怎么让一个序列流合并为单个值(folding)：

	NSLog(@"%@",[[[array rac_sequence] map:^id (id value){
                    return [value stringValue];
                }] foldLeftWithStart:@"" reduce:^id (id accumulator, id value){
                    return [accumulator stringByAppendingString:value];
            }]);

因此，我们可以非常方便地使用 RACSequence 来实现集合的链式操作，直到得到你想要的最终结果为止。除此之外，使用 RACSequence 的另外一个主要好处是，RACSequence 中包含的值在默认情况下是懒计算的，即只有在真正用到的时候才会被计算，并且只会计算一次。也就是说，如果我们只用到了一个 RACSequence 中的部分值的时候，它就在不知不觉中提高了我们应用的性能。

同样的，RACSequence 的一系列功能也是通过类簇来实现的，它共有九个用来实现不同功能的私有子类。RACSequence 为类簇提供了统一的对外接口，对于使用它的客户端代码来说，完全不需要知道私有子类的存在，很好地隐藏了实现细节。另外，值得一提的是，RACSequence 实现了快速枚举的协议 NSFastEnumeration ，在这个协议中只声明了一个看上去非常抽筋的方法：
	
	- (NSUInteger)countByEnumeratingWithState:(NSFastEnumerationState *)state objects:(id __unsafe_unretained [])buffer count:(NSUInteger)len;
有兴趣的同学，可以看看 RACSequence 中的相关实现，我们将会在后续的文章中进行介绍。因此，我们也可以直接使用 for in 来遍历一个 RACSequence 。

- 跟`BlockKit`类似
- 看源码感觉性能不是很好，有待验证。对于几个、几十个的小数组，应该问题不大




### 订阅者`RACSubscriber`

### 调度器`RACScheduler`

### 清洁工`RACDisposable`

### `RACCommand`

[ReactiveCocoa Essentials: Understanding and Using RACCommand](http://codeblog.shape.dk/blog/2013/12/05/reactivecocoa-essentials-understanding-and-using-raccommand/)




## ReactiveCocoa对富途牛牛的一些启发

### 界面控件绑定
将控件emailTextField内容赋值给self.viewModel.email

	RAC(self.viewModel, email) = self.emailTextField.rac_textSignal;  
	
将self.viewModel.statusMessage显示到statusLabel控件

  	RAC(self.statusLabel, text) =RACObserve(self.viewModel, statusMessage);  
  	
  
### 统一处理所有异步的行为
使用信号来代表这些异步事件，提供了一种统一的方式来处理所有异步的行为，包括代理方法、`block` 回调、`target-action 机制`、通知、`KVO` 等

富途牛牛项目：

- 解决KVO订阅忘记取消订阅的问题
- 解决订阅通知忘记取消的问题
    	
### 链式依赖的操作
 
 依赖关系通常出现在网络请求中,如后一个请求应该等前一个请求完成后再创建,等等:
 
 	[client logInWithSuccess:^{
    	[client loadCachedMessagesWithSuccess:^(NSArray *messages) {
        	[client fetchMessagesAfterMessage:messages.lastObject 	success:^(NSArray *nextMessages) {
           	 NSLog(@"Fetched all messages.");
        	} failure:^(NSError *error) {
            	[self presentError:error];
        	}];
    	} failure:^(NSError *error) {
        	[self presentError:error];
   	 	}];
	} failure:^(NSError *error) {
    	[self presentError:error];
	}];

ReactiveCocoa 可以特别方便地处理这种逻辑模式:

	[[[[client logIn]
   	 	then:^{
        	return [client loadCachedMessages];
    	}]
    	flattenMap:^(NSArray *messages) {
        	return [client fetchMessagesAfterMessage:messages.lastObject];
    	}]
    	subscribeError:^(NSError *error) {
        	[self presentError:error];
    	} completed:^{
        	NSLog(@"Fetched all messages.");
    	}];
 
TCP短连接可以这样优化

	[[[[service connect] flattenMap:^RACStream *(id value) {
    	return [service doSomething1];
	}] flattenMap:^RACStream *(id something1Value) {
    	// if doSomething1 is successful, 	'somethingValue' is passed via sendNext
    	return [service disconnect];
	}] subscribeError:^(NSError *error) {
    	// Error occurred!  Handle "error" if necessary.
	} completed:^{
    	// Asynchronous chain of operations succeeded.
	}];
 
在异步操作上使用signals信号,让通过链接和转换这些signal信号,构建更加复杂的行为成为可能.可以在一组操作完成后,来触发此操作即可:

	// 执行两个网络操作,并在它们都完成后在控制台打印信息.
	//
	// +merge: 传入一组signal信号,并返回一个新的RACSignal信号对象.这个新返回的RACSignal信号对象,传递所有请求的值,并在所有的请求完成时完成.即:新返回的RACSignal信号,在每个请求完成时,都会发送个消息;在所有消息完成时,除了发送消息外,还会触发"完成"相关的block.
	//
	// -subscribeCompleted: signal信号完成时,将会执行block.
	[[RACSignal 
    	merge:@[ [client fetchUserRepos], [client fetchOrgRepos] ]] 
    	subscribeCompleted:^{
       	 NSLog(@"They're both done!");
    	}];
    

 富途牛牛项目，将很多异步操作做成独立的RACSigal, 方便单元测试，方便组织先后顺序、依赖关系。比如优化启动流程、登录流程。
 
### 网络请求避免过于频繁
股票网络搜索

	 [[[[[[[[[self.textField.rac_textSignal
            filter:^BOOL(id value) { //过滤
                return [value length] > 0;
            }]
           throttle:0.3] //无任何输入0.3秒后继续
          logAll]
         flattenMap:^id(id value) { //flattenMap有新的搜索请求后，上一次网络请求结果会被忽略
             return [self search:value];
         }]
        logAll]
       map:^id(NSArray *array) {
           return [[[array rac_sequence] map:^id(id value) {
               Stock *stock = [[Stock alloc] init];
               stock.name = value[@"name"];
               stock.symbol = value[@"symbol"];
               stock.exch = value[@"exchDisp"];
               return stock;
           }] array];
       }]
      logAll]
     deliverOn:[RACScheduler mainThreadScheduler]]
     subscribeNext:^(id x) {
         self.stocks = x;
     } error:^(NSError *error) {
         
     } completed:^{
         
     }];

## 一些坑

### block要记得用strongify/weakify 

因为RAC很多操作都是在Block中完成的，这块最常见的问题就是在block直接把self拿来用，造成block和self的retain cycle。所以需要通过@strongify和@weakify来消除循环引用。

有些地方很容易被忽略，比如RACObserve(thing, keypath)，看上去并没有引用self，所以在subscribeNext时就忘记了weakify/strongify。但事实上RACObserve总是会引用self，即使target不是self，所以只要有RACObserve的地方都要使用weakify/strongify。

### RACObserve(self, model.title) 与 RACObserve(self.model, title)

    // 描述: self 本身有一个title属性和一个model属性,model本身也有一个title属性.
    RAC(self, title, @"") = RACObserve(self, model.title);
    RAC(self, title, @"") = RACObserve(self.model, title);
 
这两行代码,有着质的不同!

    RAC(self, title, @"") = RACObserve(self, model.title);
 
适用场景: self的model属性改变时,动态改变self自身title属性的值,其值为新model的title属性.

    RAC(self, title, @"") = RACObserve(self.model, title);
 
适用场景: self的model属性的title属性改变时,动态改变self自身title属性的值,其值为原有model的title属性.

## 参考链接
- [ReactiveCocoa,最受欢迎的iOS函数响应式编程库(2.5版),没有之一!](http://www.ios122.com/2015/10/reactivecocoa/)
- [Basic operations examples](https://yalantis.com/blog/reactive-programming-on-objective-c/)
- [ReactiveCocoa2实战](http://limboy.me/ios/2014/06/06/deep-into-reactivecocoa2.html)
- [最快让你上手ReactiveCocoa之进阶篇 常见操作方法介绍](http://www.jianshu.com/p/e10e5ca413b7)
- [ReactiveCocoa Tutorial – The Definitive Introduction: Part 1/2](https://www.raywenderlich.com/62699/reactivecocoa-tutorial-pt1)
- [ReactiveCocoa Tutorial – The Definitive Introduction: Part 2/2](https://www.raywenderlich.com/62796/reactivecocoa-tutorial-pt2)


- [美团技术博客 ReactiveCocoa分类](http://tech.meituan.com/tag/ReactiveCocoa)
- [reactivecocoa2-源码浅析](http://nathanli.cn/2015/08/27/reactivecocoa2-源码浅析/)
- [ReactiveCocoa 讨论会 - 唐巧的技术博客](http://gold.xitu.io/entry/568bd2ae60b2e57ba2cd2c7b)
- [Learn Haskell](http://learnyouahaskell.com)
- [Coursea 响应式程序设计原理](https://www.coursera.org/course/reactive)