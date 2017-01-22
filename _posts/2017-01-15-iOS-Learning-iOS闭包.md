---
layout: post
title: "iOS-Learning-iOS闭包"
date: 2017-01-22
excerpt: "iOS-Learning-iOS闭包"
tags: [iOS Block]
comments: true
---


#### 目录 ####
* 什么是闭包
* 匿名函数
* 闭包语法
* 自动变量
* __Block关键字
* 为什么要使用闭包
* 函数作用域与块作用域

## 什么是闭包
 > 简单来说，就是带有自动变量(本地)的匿名函数。
 
## 匿名函数
 顾名思义，就是没有名字的函数。在ObjC中，匿名函数需要依靠block(闭包)来实现。
 
 在C语言中:
 
 {% highlight c %}
  void func(int a,int b) {
  	printf("a+b=%d\n",a+b);
  }
  
  void main() {
  	func(1,2);
  }
 {% endhighlight %}

 在Objc中，我们可以使用匿名函数的来实现:
 
 {% highlight objc %}
 -(void)main() {
 	^(void)(int a,int b) {
 		printf("a+b=%d\n",a+b);
 	}(1,2);
 }
 {% endhighlight %}
 
### 闭包语法

> ^ return type (argument list) {expressions} 
 
  代码如下:
  
  {% highlight objc %}
   ^int(int count) {
	 return count+1;   
   }
  {% endhighlight %}
 

   如果返回类型是void的话,你可以这样简写：
   
   {% highlight objc %}
   ^(int count) {
	 return count+1;   
   }
  {% endhighlight %}
  
  如果函数参数为空的话，你可以这样简写：
  
   {% highlight objc %}
   ^ {
	 return 1;   
   }
  {% endhighlight %}
  
### 自动变量
让我们先来看一段代码：
{% highlight objc %}
int main() {	int val = 10;	const char *fmt = "val = %d\n";	void (^blk)(void) = ^{		printf(fmt, val); 	};	val = 2;	fmt = "These values were changed. val = %d\n";	blk();	return 0;}
{% endhighlight %}

Ques:你觉得执行结果会是什么呢？ 

Answer:These values were changed. val = 10 ?

Answer:实际输出的是 val = 10。

Ques:为什么呢？ 

Answser: 
 在源码中，声明了变量fmt和val。自动变量的值将会在闭包表达式编写时候记录，当闭包执行的时候，当时的值会一直保存着，即使你后面修改了变量值，闭包中的变量并未受影响。
 以上就是记录自动变量。
 
### Block关键字
 当自动变量被记录时候，在闭包中的值都是只读的，你无法修改里面的值。
 __block关键字，则使得你能够修改自动变量而不是记录他们。
 
 {% highlight objc %}
 int main() {	NSLog(@"Hello, Block!");	__block int val = 10;	__block const char *fmt = "val = %d\n";	void (^blk)(void) = ^{printf(fmt, val);};	val = 2;	fmt = "These values were changed. val = %d\n"; blk();	return 0; }
 {% endhighlight %}
 
 Answer:这样就输出: These values were changed。 val = 2
 
 Ques: 等等，我貌似发现了一点端倪。
 
 Answer: 竟然被你知道了。
 
 Ques: 我觉得这和C语言的传值和传地址很类似，__block关键字修饰变量的时候，在闭包中并不是记录值，而是地址。
 
 Answer: 对! 这就是它的内部实现。
 
### 为什么要使用闭包呢

使用闭包，你不需要在全局创建名称函数，或者提供目标-动作回调，可以通过创建一个匿名的本地"方法"，他能够访问作用域内的变量和更加方便执行动作。

### iOS是函数作用域还是块作用域

  任何一对花括号（｛和｝）中的语句集都属于一个块，在这之中定义的所有变量在代码块外都是不可见的，我们称之为块级作用域。 函数作用域就好理解了，定义在函数中的参数和变量在函数外部是不可见的。
  所以iOS是块作用域，而javascript则是[函数作用域](https://yanci.me/JavaScript-Learning-js%E9%97%AD%E5%8C%85/)。
 
### End 

 


