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

应用例子

> 一个一般的例子是你在餐厅定餐。 你(i.e. Client) 问服务员 (i.e. Invoker) 去买一些食物 (i.e. Command) 和 服务员传递请求给知道烹饪什么和如何烹饪的厨师 (i.e. Receiver)。 另外一个例子是你(i.e. Client) 使用遥控器来控制 (i.e. Command) 电视 (i.e. Receiver)

简单地说

> 允许你对目标封装行为。它的主要目的是提供工具来解耦客户以及接受者。

维基百科说

> 在面向对象编程中，命令模式是行为设计模式，一个目标被封装成包含所有的信息来完成动作以及延迟触发一个事件。这个信息包含方法名，这个目标拥有该方法以及方法参数的目标

程序例子
首先我们有一个接受者，它拥有所有动作的实现以及能被执行


{% highlight java %}

// Receiver
class Bulb
{
public function turnOn()
{
echo "Bulb has been lit";
}

public function turnOff()
{
echo "Darkness!";
}
}

{% endhighlight %} 

然后我们有一个接口，它包含每一个被实现的命令以及我们有一个目标集合

{% highlight java %}
interface Command
{
public function execute();
public function undo();
public function redo();
}

// Command
class TurnOn implements Command
{
protected $bulb;

public function __construct(Bulb $bulb)
{
$this->bulb = $bulb;
}

public function execute()
{
$this->bulb->turnOn();
}

public function undo()
{
$this->bulb->turnOff();
}

public function redo()
{
$this->execute();
}
}

class TurnOff implements Command
{
protected $bulb;

public function __construct(Bulb $bulb)
{
$this->bulb = $bulb;
}

public function execute()
{
$this->bulb->turnOff();
}

public function undo()
{
$this->bulb->turnOn();
}

public function redo()
{
$this->execute();
}
}

{% endhighlight %} 

然后我们有一个Invoker，客户能够调用它来处理所有的命令

{% highlight java %}
// Invoker
class RemoteControl
{
public function submit(Command $command)
{
$command->execute();
}
}

{% endhighlight %} 

最后 让我们看如何使用

{% highlight java %}
$bulb = new Bulb();

$turnOn = new TurnOn($bulb);
$turnOff = new TurnOff($bulb);

$remote = new RemoteControl();
$remote->submit($turnOn); // Bulb has been lit!
$remote->submit($turnOff); // Darkness!


{% endhighlight %} 

命令模式能够被使用来完成一个事务系统。当你保存执行过的历史命令。如果最后一个命令成功执行，
这是很棒的，否则你可以迭代这个命令历史来为所有执行过的命令执行undo操作。


### Iterator 
应用例子

> 老式的电台是一个很好的迭代器例子，用户从某个频道开始，按上一个或者下一个按钮来收听对应的频道。或者举一个MP3播放器的例子，或者举一个TV电视的例子，你可以按上一个和下一个按钮来顺序的浏览频道。换句话说，他们提供同一个接口来迭代浏览对应的频道，歌曲或者音频频道。

简单的说 

> 它提供一个方式来访问目标的元素，但并不暴露底层的实现。

维基百科说

> 在面向对象编程中，迭代器模式是一个迭代器用来遍历容器以及访问容器里的元素。迭代者模式从容器中解耦算法；在某些例子中，算法是容器特定必要的以及无法被解耦的。

程序例子

在PHP中，很容易使用SPL(Standard PHP Library)来实现。将上面的电台例子翻译一下。首先我们有一个**RadioStation**

{% highlight java %}
class RadioStation
{
protected $frequency;

public function __construct(float $frequency)
{
$this->frequency = $frequency;
}

public function getFrequency(): float
{
return $this->frequency;
}
}

{% endhighlight %} 

然后我们有着自己的迭代器

{% highlight java %}
use Countable;
use Iterator;

class StationList implements Countable, Iterator
{
/** @var RadioStation[] $stations */
protected $stations = [];

/** @var int $counter */
protected $counter;

public function addStation(RadioStation $station)
{
$this->stations[] = $station;
}

public function removeStation(RadioStation $toRemove)
{
$toRemoveFrequency = $toRemove->getFrequency();
$this->stations = array_filter($this->stations, function (RadioStation $station) use ($toRemoveFrequency) {
return $station->getFrequency() !== $toRemoveFrequency;
});
}

public function count(): int
{
return count($this->stations);
}

public function current(): RadioStation
{
return $this->stations[$this->counter];
}

public function key()
{
return $this->counter;
}

public function next()
{
$this->counter++;
}

public function rewind()
{
$this->counter = 0;
}

public function valid(): bool
{
return isset($this->stations[$this->counter]);
}
}

{% endhighlight %} 

然后它能这样来使用
{% highlight java %}
$stationList = new StationList();

$stationList->addStation(new RadioStation(89));
$stationList->addStation(new RadioStation(101));
$stationList->addStation(new RadioStation(102));
$stationList->addStation(new RadioStation(103.2));

foreach($stationList as $station) {
echo $station->getFrequency() . PHP_EOL;
}

$stationList->removeStation(new RadioStation(89)); // Will remove station 89
{% endhighlight %} 

### Mediator (Wait ...)

### Memento

### Observer

### Visitor

### Strategy

### State

### Template Method

### Wrap Up Folks

### Contribution

### License
