---
layout: post
title: "OpenSource ReactiveCocoa"
date: 2017-01-22
excerpt: "OpenSource ReactiveCocoa"
tags: [iOS opensource reactivecocoa FRP]
comments: true
---

### 目录 ###
* 函数响应式编程(FRP)
* 控制流和数据流
* Pull-Driven 和 Push-Driven
* ReactiveObjc
	* Streams
	* Signals
	* Subscription
	* Subjects
	* Commands
	* Connections
	* Sequences
	* Disposables
	* Shedulers
	* ValueType 
* Use ReactiveObjc in MVVM

### 函数响应式编程 ###
 * [命令式编程](https://zh.wikipedia.org/wiki/%E6%8C%87%E4%BB%A4%E5%BC%8F%E7%B7%A8%E7%A8%8B)  
 * [函数式编程](https://zh.wikipedia.org/wiki/%E5%87%BD%E6%95%B8%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80)
 * [响应式编程](https://zh.wikipedia.org/wiki/%E5%93%8D%E5%BA%94%E5%BC%8F%E7%BC%96%E7%A8%8B)
 * [函数响应式编程](https://en.wikipedia.org/wiki/Functional_reactive_programming)

### 热信号和冷信号 ###
冷信号：除非有人订阅他们，他们是不会启动并发送的。每增加一个订阅，他们都会重复地多发送一个信号。

热信号： 立即工作的信号。


### pull-driven 和 push-driven ###
push-driven: 在创建信号的时候，信号不会被立即赋值，之后才会赋值(举个例子:网络请求回来的结果或者是任意的用户输入的结果)。

pull-driven: 在创建信号的同事序列中的值就会被确定下来，我们可以从流中一个个地查询值。

### ReactiveObjc ###
* Streams  
* Signals
* Subscription
* Subjects
* Commands
* Connections
* Sequences
* Disposables
* Shedulers
* ValueType 


### Use ReactiveObjc in MVVM ###



### Reference ###

[http://www.infoq.com/cn/articles/functional-reactive-programming](http://www.infoq.com/cn/articles/functional-reactive-programming)
[https://github.com/ReactiveCocoa/ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa)


 
 
 
