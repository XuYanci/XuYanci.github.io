---
layout: post
title: "Translate-iOS-DesignPatterns(3)"
date: 2017-04-04
excerpt: "Translate-iOS-DesignPatterns(3)"
tags: [designpatterns]
comments: true
---

## 行为型设计模式

简单的说

> 它是有关对象之间的责任分配。它们和结构型设计模式不同的是它们不定义结构但是也概述了相关模式用来做它们之间的消息传递/通讯。用另外的一句话说，它们有助于回答 "如何在软件元件中运行行为?"

维基百科说

> 在软件工程中，行为型设计模式是代表对象之间常用的通讯模式以及模式实现。通过这么做的话，这些模式实现这些通讯会提高复杂度。

* 责任链
* 命令
* 迭代者
* 中间者
* 备忘录
* 观察者
* 访问者
* 策略模式
* 状态模式
* 模板方法模式

### 责任链模式

应用例子

>   举个例子，你的账号有三个方式付款（A，B，和C）；他们中都有一个不同的账号。A有100USD，B有300USD和C有1000USD ， 付款的优先级是A，B，C。你购买价值210USD的东西。通过使用责任链模式，首先账号A如果能够购买的话就扣钱，响应链断开。如果不够，则继续换到账号B检查是否足够扣钱，同理按照这个方式知道找到合适的处理。这里A，B，和C都是链的链接以及责任链的所有现象。

简单的说

>   它帮助构建一系列目标。请求从一端发起，然后一个一个目标寻找，直到找到合适的处理。
    
维基百科说

>   在面向对象设计中，责任链模式是由命令目标源以及一系列处理目标组成的。每一个处理目标包含着定义命令目标所能处理的类型；剩下的传递给链中的下一个处理目标

程序例子

翻译我们上面的账号例子。首先我们有一个基础账号，里面包含着串联账号到一起的逻辑以及某些账号。

{% highlight java %}

abstract class Account
{
protected $successor;
protected $balance;

public function setNext(Account $account)
{
$this->successor = $account;
}

public function pay(float $amountToPay)
{
if ($this->canPay($amountToPay)) {
echo sprintf('Paid %s using %s' . PHP_EOL, $amountToPay, get_called_class());
} elseif ($this->successor) {
echo sprintf('Cannot pay using %s. Proceeding ..' . PHP_EOL, get_called_class());
$this->successor->pay($amountToPay);
} else {
throw new Exception('None of the accounts have enough balance');
}
}

public function canPay($amount): bool
{
return $this->balance >= $amount;
}
}

class Bank extends Account
{
protected $balance;

public function __construct(float $balance)
{
$this->balance = $balance;
}
}

class Paypal extends Account
{
protected $balance;

public function __construct(float $balance)
{
$this->balance = $balance;
}
}

class Bitcoin extends Account
{
protected $balance;

public function __construct(float $balance)
{
$this->balance = $balance;
}
}
Now let's prepare the chain using the links defined above (i.e. Bank, Paypal, Bitcoin)

// Let's prepare a chain like below
//      $bank->$paypal->$bitcoin
//
// First priority bank
//      If bank can't pay then paypal
//      If paypal can't pay then bit coin

$bank = new Bank(100);          // Bank with balance 100
$paypal = new Paypal(200);      // Paypal with balance 200
$bitcoin = new Bitcoin(300);    // Bitcoin with balance 300

$bank->setNext($paypal);
$paypal->setNext($bitcoin);

// Let's try to pay using the first priority i.e. bank
$bank->pay(259);

// Output will be
// ==============
// Cannot pay using bank. Proceeding ..
// Cannot pay using paypal. Proceeding ..:
// Paid 259 using Bitcoin!

{% endhighlight %} 

### 命令模式

continue ... 







