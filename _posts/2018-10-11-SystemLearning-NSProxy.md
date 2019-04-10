---
layout: post
title: "iOS-Learning-NSProxy"
date: 2018-10-11
excerpt: "iOS-Learning-NSProxy"
tags: [nsproxy runtime]
comments: true
---

# SystemLearning~NSProxy


# Have an Bug!

NSTimer初始化的时候有一个方法叫做scheduledTimerWithTimeInterval，这个方法使用中需要传一个target进去，一般我们是传self进去，但是这个方法会导致一个问题产生，就是NSTime内部机制是会强引用该self，使用不当会导致循环引用，无法释放。 

我们看一下官方文档说明:


```
+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)ti 
target:(id)aTarget 
selector:(SEL)aSelector 
userInfo:(id)userInfo 
repeats:(BOOL)yesOrNo;

target
The object to which to send the message specified by aSelector when the timer fires. The timer maintains a strong reference to target until it (the timer) is invalidated.
```


从官方文档说明中，我们可以看出timer会强引用target直到它被invalidated。


# How to resolve It ?

我们可以在界面消失ViewDidDisappear的时候invaliated定时器，打破循环引用，这样就能够正常释放内存，但是这种做法不是唯一的做法，也感觉有点怪怪的。

还有一种做法就是，我们怎么让self变成weakSelf的方式。


```


typeof(self) weakSelf = self
[NSTimer scheduledTimerWithTimeInterval:1 target:weakSelf selector:@selector(tick) userInfo:nil repeats:true];
```


我们知道传到target中的self，会被NSTimer内部强引用retain+1，类似于这样: 


```
class NSTimer: NSObject {

}

@property (nonmatic, strong) NSObject *target;
```


所以并没有什么用，那如果是这样子处理呢，因为timer会持有self (retain+1)，那我就给他一个代理类(实现了tick方法,做一个判断调用）如图: 



 


![alt_text](/assets/img/SystemLearning-NSProxy0.png "image_tooltip")



## XYProxy类文件


```
@implementation XYProxy {
//    __weak id _target;
__weak ViewController1 *_vc1;
}

- (instancetype)initWithTarget:(id)target {
_target = target;
return self;
}

+ (instancetype)proxyWithTarget:(id)target {
return [[XYProxy alloc] initWithTarget:target];
}

- (instancetype)initWithTarget1:(ViewController1 *)target {
_vc1 = target;
return self;
}

- (void)tick {
if (_vc1 != nil) {
[_vc1 tick];
} else {
NSLog(@"oh my tick!");
}
}
```



## ViewController1类文件:


```
@implementation ViewController1

- (void)viewDidLoad {
[super viewDidLoad];
// Do any additional setup after loading the view.
XYProxy *proxy = [[XYProxy alloc]initWithTarget1:self];
[NSTimer scheduledTimerWithTimeInterval:1 target:proxy selector:@selector(tick) userInfo:nil repeats:true];
}

- (void)didReceiveMemoryWarning {
[super didReceiveMemoryWarning];
// Dispose of any resources that can be recreated.
}

- (void)tick {
NSLog(@"tick");
}

- (void)dealloc {
NSLog(@"dealloc");
}
```


通过源代码我们可以看到，NSTimer会先调用代理类的XYProxy.tick方法，然后判断target是否为空（weak属性,是否释放，如果释放则为nil)，从而调用实际的target方法。  

运行一下看看: 




![alt_text](/assets/img/SystemLearning-NSProxy1.png "image_tooltip")


当ViewController1 pop推出去的时候，能够正常释放，然后代理类其实是未释放的 (autorelease pool里面?) ，但是这个不会引起太大的影响。

但是这样做有一个问题，就是我的RealTarget和ProxyTarget都得实现通用一个方法tick.

我们怎么来解决这个问题呢，我们知道Objc是属于runtime的机制，我们可以利NSProxy(也叫代理)的这个类来达到我们的目的。


# NSProxy ! 

我们看一下官方文档的介绍:

 


![alt_text](/assets/img/SystemLearning-NSProxy2.png "image_tooltip")


大概说的是， 消息传递到NSProxy，通常会被传递到一个Real Object 。 

NSProxy是一个抽象类，它基本上提供了定义在NSObject协议中所需的方法。(我们知道ObjC中的对象都是继承NSObject的) 

实现它的话，你需要覆写 forwardInvocation: 以及 methodSignatureForSelector: 这两个方法。


# Example:


```
@implementation XYProxy {
__weak id _target;
}

- (instancetype)initWithTarget:(id)target {
_target = target;
return self;
}

+ (instancetype)proxyWithTarget:(id)target {
return [[XYProxy alloc] initWithTarget:target];
}

- (id)forwardingTargetForSelector:(SEL)selector {
return _target;
}

- (void)forwardInvocation:(NSInvocation *)invocation {
void *null = NULL;
[invocation setReturnValue:&null];
}

- (NSMethodSignature *)methodSignatureForSelector:(SEL)selector {
return [NSObject instanceMethodSignatureForSelector:@selector(init)];
}
```

![alt_text](/assets/img/SystemLearning-NSProxy3.png "image_tooltip")


这个实现思路和前一个Example实现思路差不多，传入一个target，然后__weak 引用指向target, 最重要的是利用了NSProxy转发的特性  `forwardingTargetForSelector 指向真实target 来执行调用方法。当target为null时候，则执行另外其他两个方法（相对于不执行任何操作）。`


# 参考

[https://developer.apple.com/documentation/foundation/nstimer/1412416-scheduledtimerwithtimeinterval](https://developer.apple.com/documentation/foundation/nstimer/1412416-scheduledtimerwithtimeinterval)

[http://ggghub.com/2016/05/11/%E5%88%A9%E7%94%A8NSProxy%E8%A7%A3%E5%86%B3NSTimer%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F%E9%97%AE%E9%A2%98/](http://ggghub.com/2016/05/11/%E5%88%A9%E7%94%A8NSProxy%E8%A7%A3%E5%86%B3NSTimer%E5%86%85%E5%AD%98%E6%B3%84%E6%BC%8F%E9%97%AE%E9%A2%98/)

[https://developer.apple.com/documentation/foundation/nsproxy](https://developer.apple.com/documentation/foundation/nsproxy)

