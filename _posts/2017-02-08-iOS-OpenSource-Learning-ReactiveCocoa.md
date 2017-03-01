---
layout: post
title: "iOS-OpenSource-ReactiveCocoa"
date: 2017-01-22
excerpt: "iOS-OpenSource-ReactiveCocoa"
tags: [reactivecocoa ios]
comments: true
---

### 目录 ###
* 函数响应式编程(FRP)
* 热信号和冷信号
* Pull-Driven 和 Push-Driven
* Use ReactiveObjc in MVVM

### 函数响应式编程 ###
 * [命令式编程](https://zh.wikipedia.org/wiki/%E6%8C%87%E4%BB%A4%E5%BC%8F%E7%B7%A8%E7%A8%8B)  
 * [函数式编程](https://zh.wikipedia.org/wiki/%E5%87%BD%E6%95%B8%E7%A8%8B%E5%BC%8F%E8%AA%9E%E8%A8%80)
 * [响应式编程](https://zh.wikipedia.org/wiki/%E5%93%8D%E5%BA%94%E5%BC%8F%E7%BC%96%E7%A8%8B)
 * [函数响应式编程](https://en.wikipedia.org/wiki/Functional_reactive_programming)

### 流 ###
流是值的序列化的抽象，你可以认为一个流就像一条水管，而值就是流淌在水管中的水，值从管道的一端流入从另一端流出。当值从管道的另一端流出的时候，我们可以读取过去所有的值，甚至是刚刚进入管道的值（即当前值）。 
流分为信号流和序列流。


### pull-driven 和 push-driven ###
push-driven: 在创建信号的时候，信号不会被立即赋值，之后才会赋值(举个例子:网络请求回来的结果或者是任意的用户输入的结果)。

pull-driven: 在创建信号的同事序列中的值就会被确定下来，我们可以从流中一个个地查询值。


#### 序列流 ####
序列，默认情况下是延迟加载的（也称：懒加载或被动加载），是pull-driven的，在他们被生成的时候就会提供确切的值，而数组方法会强制给序列的每一个成员赋值。

#### 信号流  ####

信号是另一种类型的流，和序列流相反，信号是push-driven的。新的值能够通过管道发布但不能像pull-driven一样在管道中获取，他们所抽象出来的数据会在未来的某个时间传送过来。

### 热信号和冷信号 ###

冷信号：除非有人订阅他们，他们是不会启动并发送的。每增加一个订阅，他们都会重复地多发送一个信号。

热信号： 立即工作的信号。


### Use ReactiveObjc in MVVM ###

接下来我们来剖析FRP这几个模块在MVVM的实现：

* 登录模块
* 瀑布流模块
* 图片浏览模块
* 图片详情模块

#### 登录模块 ####

界面UI:

![Mou Icon](/assets/img/2017-01-22-opensource_reactivecocoa/frp_login.png)


我们看界面UI上面主要有 
	
1.用户名输入框 

2.密码输入框 

3.取消按钮 

4.登录按钮

业务逻辑主要是
    
1.实现用户登录(登录成功、登录失败后面操作)

2.登录按钮在用户名以及密码输入不为空显示可操作状态。













### Reference ###

[http://www.infoq.com/cn/articles/functional-reactive-programming](http://www.infoq.com/cn/articles/functional-reactive-programming)
[https://github.com/ReactiveCocoa/ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa)


 
 
 
