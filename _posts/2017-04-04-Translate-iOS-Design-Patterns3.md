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

### Mediator 

应用例子

> 一个普通的例子就是当你通过电话和别人说话的时候，有一个网络提供者在你们的会话中间，你们说话并不是直接传送的，在这个例子中网络提供者就就是中介者。

简单的说

> 中介者模式添加第三方对象(称为中介者) 来控制两个目标之间的交互(称为同伴)。它主要是解耦类之间的通讯。因为现在他们并不需要知道各自的实现。

维基百科说

> 在软件工程中，中介者模式封装了目标间之间的交互。这个模式被认为是一个行为模式是因为它能够改变程序运行中的行为。

程序例子

这是一个最简单的聊天室例子(i.e. 中介者) ，用户(i.e. 同伴)之间互相发送消息

首先，我们有一个中介者.i.e.聊天室

{% highlight java %}
interface ChatRoomMediator 
{
public function showMessage(User $user, string $message);
}

// Mediator
class ChatRoom implements ChatRoomMediator
{
public function showMessage(User $user, string $message)
{
$time = date('M d, y H:i');
$sender = $user->getName();

echo $time . '[' . $sender . ']:' . $message;
}
}

{% endhighlight %} 

然后我们有我们的用户i.e.同伴

{% highlight java %}
class User {
protected $name;
protected $chatMediator;

public function __construct(string $name, ChatRoomMediator $chatMediator) {
$this->name = $name;
$this->chatMediator = $chatMediator;
}

public function getName() {
return $this->name;
}

public function send($message) {
$this->chatMediator->showMessage($this, $message);
}
}
{% endhighlight %} 

以及用法

{% highlight java %}

$mediator = new ChatRoom();

$john = new User('John Doe', $mediator);
$jane = new User('Jane Doe', $mediator);

$john->send('Hi there!');
$jane->send('Hey!');

// Output will be
// Feb 14, 10:58 [John]: Hi there!
// Feb 14, 10:58 [Jane]: Hey!

{% endhighlight %} 

### Memento 
应用例子

> 举一个计算器的例子 (i.e. originator), 你在哪里完成计算的时候，最后的计算结果都会保存在内存之中 (i.e. memento) ，你可以回溯它，也可以使用某些动作按钮来存储它(i.e. caretaker)。

简单的说

> 备忘录模式是在一定程度上关于拷贝和存储目标的当前状态，他也能够以平稳的方式存储。


维基百科说 

> 备忘录模式是一个软件设计模式，能够提供恢复到之前状态的能力（undo via rollback)

在你需要提供某些取消的功能是非常有用的。

程序例子

举一个文本编辑器的例子，它能够实时保存状态，而且你能过回复你所想要的。
首先，我们有自己的备忘录对象，它能够保存编辑器状态

{% highlight java %}
class EditorMemento
{
protected $content;

public function __construct(string $content)
{
$this->content = $content;
}

public function getContent()
{
return $this->content;
}
}
{% endhighlight %} 

然后我们有我们的编辑器 i.e. originator 来使用备忘录对象

{% highlight java %}
class Editor
{
protected $content = '';

public function type(string $words)
{
$this->content = $this->content . ' ' . $words;
}

public function getContent()
{
return $this->content;
}

public function save()
{
return new EditorMemento($this->content);
}

public function restore(EditorMemento $memento)
{
$this->content = $memento->getContent();
}
}
{% endhighlight %} 

它能这么被使用
{% highlight java %}
$editor = new Editor();

// Type some stuff
$editor->type('This is the first sentence.');
$editor->type('This is second.');

// Save the state to restore to : This is the first sentence. This is second.
$saved = $editor->save();

// Type some more
$editor->type('And this is third.');

// Output: Content before Saving
echo $editor->getContent(); // This is the first sentence. This is second. And this is third.

// Restoring to last saved state
$editor->restore($saved);

$editor->getContent(); // This is the first sentence. This is second.
{% endhighlight %} 

### Observer

应用例子

> 一个好的例子是求职者在求职网站订阅工作，如果有匹配的工作机会他们将会被通知。

简单的说

> 定义一个目标之间的依赖，当目标状态被修改了，所有它的依赖都会被通知

维基百科说

> 观察者模式是一个软件设计模式，一个目标我们称为话题。保存着一个它的依赖列表，我们称为订阅者，当状态修改了会自动通知他们，一般会调用它们的犯法。



程序例子

翻译我们上面的例子。首先我们有求职者需要在工作发布时候被通知。


{% highlight java %}

class JobPost
{
protected $title;

public function __construct(string $title)
{
$this->title = $title;
}

public function getTitle()
{
return $this->title;
}
}

class JobSeeker implements Observer
{
protected $name;

public function __construct(string $name)
{
$this->name = $name;
}

public function onJobPosted(JobPost $job)
{
// Do something with the job posting
echo 'Hi ' . $this->name . '! New job posted: '. $job->getTitle();
}
}

{% endhighlight %} 

然后我们有我们的发布工作，求职者会订阅它

{% highlight java %}
class JobPostings implements Observable
{
protected $observers = [];

protected function notify(JobPost $jobPosting)
{
foreach ($this->observers as $observer) {
$observer->onJobPosted($jobPosting);
}
}

public function attach(Observer $observer)
{
$this->observers[] = $observer;
}

public function addJob(JobPost $jobPosting)
{
$this->notify($jobPosting);
}
}
{% endhighlight %} 

然后这样使用它

{% highlight java %}

// Create subscribers
$johnDoe = new JobSeeker('John Doe');
$janeDoe = new JobSeeker('Jane Doe');

// Create publisher and attach subscribers
$jobPostings = new JobPostings();
$jobPostings->attach($johnDoe);
$jobPostings->attach($janeDoe);

// Add a new job and see if subscribers get notified
$jobPostings->addJob(new JobPost('Software Engineer'));

// Output
// Hi John Doe! New job posted: Software Engineer
// Hi Jane Doe! New job posted: Software Engineer

{% endhighlight %} 

### Visitor 

应用例子

想象某个人拜访迪拜。他们只是需要一个方式进入迪拜(i.e. visa)，到了之后，他们可以自己拜访迪拜的任何地方，而不需要请求许可或者做些跑腿工作来拜访；只是让他们知道什么地方可以拜访。访问者模式让你做这些，它帮你添加地方来访问，因此它们能够被访问而不需要做其他跑腿工作。   

简单的说 

访问者模式让你添加更多操作而不需要修改他们。

维基百科说

在面向对象编程以及软件工程中，访问者设计模式是一种分离它操作的目标结构的一种算法。一个实际操作例子是为已经存在的目标添加额外操作而不需要修改这些结构。它也是一种遵循开闭原则的方式。


程序例子

让我们举一个动物园的例子，我们有着不同种类的动物以及我们让它们发出声音。让我们使用访问者模式来转换它。


{% highlight java %}
// Visitee
interface Animal
{
public function accept(AnimalOperation $operation);
}

// Visitor
interface AnimalOperation
{
public function visitMonkey(Monkey $monkey);
public function visitLion(Lion $lion);
public function visitDolphin(Dolphin $dolphin);
}
{% endhighlight %} 

然后我们有动物们的实现

{% highlight java %}
class Monkey implements Animal
{
public function shout()
{
echo 'Ooh oo aa aa!';
}

public function accept(AnimalOperation $operation)
{
$operation->visitMonkey($this);
}
}

class Lion implements Animal
{
public function roar()
{
echo 'Roaaar!';
}

public function accept(AnimalOperation $operation)
{
$operation->visitLion($this);
}
}

class Dolphin implements Animal
{
public function speak()
{
echo 'Tuut tuttu tuutt!';
}

public function accept(AnimalOperation $operation)
{
$operation->visitDolphin($this);
}
}

{% endhighlight %} 

继续完成我们的访问者

class Speak implements AnimalOperation
{
public function visitMonkey(Monkey $monkey)
{
$monkey->shout();
}

public function visitLion(Lion $lion)
{
$lion->roar();
}

public function visitDolphin(Dolphin $dolphin)
{
$dolphin->speak();
}
}

我们可以这么使用

{% highlight java %}
$monkey = new Monkey();
$lion = new Lion();
$dolphin = new Dolphin();

$speak = new Speak();

$monkey->accept($speak);    // Ooh oo aa aa!    
$lion->accept($speak);      // Roaaar!
$dolphin->accept($speak);   // Tuut tutt tuutt!
{% endhighlight %} 

我们可以通过继承动物们来做这些，但是当我们添加新的行为的时候，我们不得不修改动物。而我们现在不需要去修改它们。例如我们需要为动物们添加跳跃动作，我们只需要简单添加一个新的拜访者。

{% highlight java %}
class Jump implements AnimalOperation
{
public function visitMonkey(Monkey $monkey)
{
echo 'Jumped 20 feet high! on to the tree!';
}

public function visitLion(Lion $lion)
{
echo 'Jumped 7 feet! Back on the ground!';
}

public function visitDolphin(Dolphin $dolphin)
{
echo 'Walked on water a little and disappeared';
}
}
{% endhighlight %} 

我们这样来使用

{% highlight java %}
$jump = new Jump();

$monkey->accept($speak);   // Ooh oo aa aa!
$monkey->accept($jump);    // Jumped 20 feet high! on to the tree!

$lion->accept($speak);     // Roaaar!
$lion->accept($jump);      // Jumped 7 feet! Back on the ground!

$dolphin->accept($speak);  // Tuut tutt tuutt!
$dolphin->accept($jump);   // Walked on water a little and disappeared
{% endhighlight %} 

### Strategy

应用例子

考虑一下排序的例子，我们完成了冒泡排序，但是当数据增长的时候，冒泡排序就变得越来越慢了。为了解决这个我们完成了快速排序。但是尽管快速排序对大数据集合比较快，但对小数据集合就变得很慢了。为了解决这个问题，我们实现一个当小数据集合时候，我们使用冒泡排序，当大数据集合时候，我们使用快速排序。

简单地说

策略模式允许你根据不同的情形来切换算法和策略

维基百科说

在计算机编程中，策略模式(也叫政策模式)是行为型模式，它允许算法行为动态选择。

程序例子

转换我们上面的例子。首先我们有我们的策略接口以及不同的策略实现


{% highlight java %}
interface SortStrategy
{
public function sort(array $dataset): array;
}

class BubbleSortStrategy implements SortStrategy
{
public function sort(array $dataset): array
{
echo "Sorting using bubble sort";

// Do sorting
return $dataset;
}
}

class QuickSortStrategy implements SortStrategy
{
public function sort(array $dataset): array
{
echo "Sorting using quick sort";

// Do sorting
return $dataset;
}
}
{% endhighlight %}

然后我们有一个我们的客户来实现任何策略

{% highlight java %}
class Sorter
{
protected $sorter;

public function __construct(SortStrategy $sorter)
{
$this->sorter = $sorter;
}

public function sort(array $dataset): array
{
return $this->sorter->sort($dataset);
}
}
{% endhighlight %}

它能这么使用

{% highlight java %}
$dataset = [1, 5, 4, 3, 2, 8];

$sorter = new Sorter(new BubbleSortStrategy());
$sorter->sort($dataset); // Output : Sorting using bubble sort

$sorter = new Sorter(new QuickSortStrategy());
$sorter->sort($dataset); // Output : Sorting using quick sort
{% endhighlight %}


### State

应用例子

> 想象你在使用某些绘图程序，你选择画刷来绘制。现在画刷根据选择的颜色来改变它的行为 i.e.。如果你已经选择了红色，它就会是红色，如果是蓝色，它就会是蓝色。

简单的说

> 它让你在状态改变的时候改变类行为。

维基百科说

> 状态模式是行为软件设计模式，它使用一种面向对象的方式实现状态机。使用状态模式，通过实现不同的状态作为状态模式接口的派生类来实现，通过调用在状态父类的方法实现状态转换。状态模式可以被解释为策略模式，它通过调用定义在模式中的接口来切换当前的状态。

程序例子


> 让我们举一个文本编辑器的例子，它让你改变已经编辑文本的状态 i.e. 如果你选择粗体，它就会是粗体，如果选择细体，他就是细体 etc.

首先我们有我们的状态接口以及某些状态实现

{% highlight java %}
interface WritingState
{
public function write(string $words);
}

class UpperCase implements WritingState
{
public function write(string $words)
{
echo strtoupper($words);
}
}

class LowerCase implements WritingState
{
public function write(string $words)
{
echo strtolower($words);
}
}

class Default implements WritingState
{
public function write(string $words)
{
echo $words;
}
}
{% endhighlight %}

然后我们有自己的编辑器

{% highlight java %}
class TextEditor
{
protected $state;

public function __construct(WritingState $state)
{
$this->state = $state;
}

public function setState(WritingState $state)
{
$this->state = $state;
}

public function type(string $words)
{
$this->state->write($words);
}
}
{% endhighlight %}

然后我们这样使用

{% highlight java %}
$editor = new TextEditor(new Default());

$editor->type('First line');

$editor->setState(new UpperCase());

$editor->type('Second line');
$editor->type('Third line');

$editor->setState(new LowerCase());

$editor->type('Fourth line');
$editor->type('Fifth line');

// Output:
// First line
// SECOND LINE
// THIRD LINE
// fourth line
// fifth line
{% endhighlight %}

### Template Method

应用例子

假如我们要建房子。建房子的步骤可能 

>

* 打地基
* 建墙壁
* 添加屋顶
* 添加地板

这些步骤的顺序不会改变。你不能在建墙壁之前建立屋顶。但是这些步骤能够被修改，例如墙壁能够是木做的，或者聚酯或者石头。

简单的说

> 模板方法定义一个能够执行确定算法的骨架。但是将实现延迟放到子类来实现。

维基百科说

> 在软件工程中，模板方法是行为设计模式用来定义在操作中算法的程序骨架，延迟这些步骤到子类实现。它让你重定义算法步骤而不需要修改到算法结构。

程序例子

> 加入我们有一个建造工具能够帮我们测试，建造以及产生建造报告(i.e. 代码覆盖报告 etc)，然后发布我们的应用到测试服务器。

首先我们有一个基类定义建造算法的骨架。

{% highlight java %}
abstract class Builder
{

// Template method
final public function build()
{
$this->test();
$this->lint();
$this->assemble();
$this->deploy();
}

abstract public function test();
abstract public function lint();
abstract public function assemble();
abstract public function deploy();
}
{% endhighlight %}

然后我们的实现


{% highlight java %}
class AndroidBuilder extends Builder
{
public function test()
{
echo 'Running android tests';
}

public function lint()
{
echo 'Linting the android code';
}

public function assemble()
{
echo 'Assembling the android build';
}

public function deploy()
{
echo 'Deploying android build to server';
}
}

class IosBuilder extends Builder
{
public function test()
{
echo 'Running ios tests';
}

public function lint()
{
echo 'Linting the ios code';
}

public function assemble()
{
echo 'Assembling the ios build';
}

public function deploy()
{
echo 'Deploying ios build to server';
}
}
{% endhighlight %}

然后我们这么使用

{% highlight java %}
$androidBuilder = new AndroidBuilder();
$androidBuilder->build();

// Output:
// Running android tests
// Linting the android code
// Assembling the android build
// Deploying android build to server

$iosBuilder = new IosBuilder();
$iosBuilder->build();

// Output:
// Running ios tests
// Linting the ios code
// Assembling the ios build
// Deploying ios build to server
{% endhighlight %}



### End 行为 ###

| 日期| 作者 | 说明 |
| ------------ | ------------- | ------------ |
| 2017-07-09 | YC | 初步翻译完成 |


