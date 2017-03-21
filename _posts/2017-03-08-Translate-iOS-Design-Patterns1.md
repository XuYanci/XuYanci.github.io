---
layout: post
title: "Translate-iOS-DesignPatterns(1)"
date: 2017-03-08
excerpt: "Translate-iOS-DesignPatterns(1)"
tags: [designpatterns]
comments: true
---

## Introduction

设计模式是针对反复出现问题的解决方式;**主旨在怎样解决特定问题**. 它们不是类，包，也不是库，你能够接入到你的程序中坐等魔法产生。然而，它们是针对特定场合某些特定问题的解决方式。

> 设计模式是用来解决重复发生问题的方式；旨在怎样解决特定问题。

维基百科词条解释

> 在软件工程中，软件设计模式是可以反复用来解决常见的在软件设计中的上下文问题。它并不是能够直接转换成源代码或者机器代码的最终设计。它只是在很多不同的场合下如何解决一个问题的描述和模板。

### Be Careful

* 设计模式并不是解决所有问题的银弹
* 别尝试去强用他们；坏事情很可能会出现，如果这样做的话。 记住设计模式使用从来解决问题的方式，不是用来发现问题的方式；因此别过度思考
* 设计模式就像一把双利刃,他们可以是用来解决问题，也可以导致糟糕的问题

> 以下代码例子是使用PHP-7来编写的，然而思想是一致的并不应该会导致你停止学习。同样也是支持其他语言。

### Types of Design Patterns

* [创建型](https://github.com/XuYanci/design-patterns-for-humans#creational-design-patterns) 
* [结构型](https://github.com/XuYanci/design-patterns-for-humans#structural-design-patterns)
* [行为型](https://github.com/XuYanci/design-patterns-for-humans#behavioral-design-patterns)


## Creational Design Patterns

简单来说
> 创建型模式主要关注点是用来初始化对象或者一组相关对象

维基百科说

> 在软件工程中，创建型设计模式针对对象创建的设计模式，旨在在特定场合下合理的创建对象。对象的创建能够导致设计问题以及增加设计复杂性。创建型设计模式通过怎样控制对象的创建来解决此类问题。

* [简单工厂](https://github.com/XuYanci/design-patterns-for-humans#-simple-factory)
* [工程方法](https://github.com/XuYanci/design-patterns-for-humans#-factory-method)
* [抽象工程](https://github.com/XuYanci/design-patterns-for-humans#-abstract-factory)
* [建造者](https://github.com/XuYanci/design-patterns-for-humans#-builder)
* [模板](https://github.com/XuYanci/design-patterns-for-humans#-prototype)
* [单例](https://github.com/XuYanci/design-patterns-for-humans#-singleton)


### 简单工厂

应用实例
> 仔细想一下，你在建一栋房子以及你需要门。如果每次你需要一个们的时候，你都穿上你的木匠衣服在你的房子里制作门那会是多么糟糕。然而你可以从工厂里面拿门。


简单的说

> 简单工厂给客户端产生一个实例，但不暴露任何实例化内部逻辑

维基百科说

> 在面向对象编程中，工厂是用来创建其他对象的对象 - 一般来说工厂是一个功能或者方法通过调用方法返回可变类型或者可变类的对象，可认为是 "new"。


#### 编程例子 ####

{% highlight java %}

interface Door
{
    public function getWidth(): float;
    public function getHeight(): float;
}

class WoodenDoor implements Door
{
    protected $width;
    protected $height;

    public function __construct(float $width, float $height)
    {
        $this->width = $width;
        $this->height = $height;
    }

    public function getWidth(): float
    {
        return $this->width;
    }

    public function getHeight(): float
    {
        return $this->height;
    }
}

{% endhighlight %}


然后我们有了门工厂能够产生门和返回它 

{% highlight java %}
class DoorFactory
{
    public static function makeDoor($width, $height): Door
    {
        return new WoodenDoor($width, $height);
    }
}
{% endhighlight %}

以及如何来使用它

{% highlight java %}
$door = DoorFactory::makeDoor(100, 200);
echo 'Width: ' . $door->getWidth();
echo 'Height: ' . $door->getHeight();
{% endhighlight %}

**什么时候使用**

当你创建一个对象并不只是一些赋值或者包括某些逻辑，将它放在一个专门的工厂里面而不是重复代码是非常有意义额。

### 工厂方法 

应用实例

> 考虑一下招聘管理的例子。一个人是不可能面试所有职位的。在公开工作的基础上，她必须针对不同人决定和委托面试步骤。

简单的说

> 它提供了一个路径来委托实例化逻辑给它的子类。

维基百科说

> 在基于类的编程中，工厂方法模式是属于创建型模式，我们使用工厂方法来处理一些对象创建问题，而不需要去指定实际创建对象的类。

编程例子

考虑一下上面我们招聘管理员的例子。首先我们有一个面试者接口和一些实现

{% highlight java %}

interface Interviewer
{
    public function askQuestions();
}

class Developer implements Interviewer
{
    public function askQuestions()
    {
        echo 'Asking about design patterns!';
    }
}

class CommunityExecutive implements Interviewer
{
    public function askQuestions()
    {
        echo 'Asking about community building';
    }
}

{% endhighlight %}

现在让我们创建我们的 *HiringManager*

{% highlight java %}

abstract class HiringManager
{

    // Factory method
    abstract public function makeInterviewer(): Interviewer;

    public function takeInterview()
    {
        $interviewer = $this->makeInterviewer();
        $interviewer->askQuestions();
      }
}

{% endhighlight %}

现在任何子类都能够扩展它以及提供所需要的面试者

{% highlight java %}
class DevelopmentManager extends HiringManager
{
    public function makeInterviewer(): Interviewer
    {
        return new Developer();
    }
}

class MarketingManager extends HiringManager
{
    public function makeInterviewer(): Interviewer
    {
        return new CommunityExecutive();
    }
}
{% endhighlight %}

我们这样使用

{% highlight java %}
$devManager = new DevelopmentManager();
$devManager->takeInterview(); // Output: Asking about design patterns

$marketingManager = new MarketingManager();
$marketingManager->takeInterview(); // Output: Asking about community building.
{% endhighlight %}

**什么时候使用？**
 
当类中需要处理东西但是所需的子类会在运行中动态确定这是非常有用的。换句话说，客户并不知道它需要什么样的子类。

### 抽象工厂

应用实例

> 从简单工厂中扩展我们的门例子。你可能需要从木门店获取木门，从铁门店获取铁门，从相关的商店获取PVC门。你或许需要一个不同的门专家，例如负责木门的木匠，负责铁门的焊接工。你可以看到门之间的一些依赖。 木门需要木匠，铁门需要焊接工等。

简单的说

> 工厂的集合；一个聚合了独立但互相依赖的工
厂而不用去指定他们的具体类的工厂。

维基百科说

> 抽象工厂模式提供一个方式，用来封装有着相同主题但不指定他们具体类的相互独立工厂集合。

程序例子

转化一下上面的门例子。首先我们有我们的*Door*接口和某些它的实现

{% highlight java %}
interface Door
{
    public function getDescription();
}

class WoodenDoor implements Door
{
    public function getDescription()
    {
        echo 'I am a wooden door';
    }
}

class IronDoor implements Door
{
    public function getDescription()
    {
        echo 'I am an iron door';
    }
}
{% endhighlight %}

然后我们有某些对应的专家对应不同的门类型

{% highlight java %}
interface DoorFittingExpert
{
    public function getDescription();
}

class Welder implements DoorFittingExpert
{
    public function getDescription()
    {
        echo 'I can only fit iron doors';
    }
}

class Carpenter implements DoorFittingExpert
{
    public function getDescription()
    {
        echo 'I can only fit wooden doors';
    }
}

{% endhighlight %}

现在我们有我们的抽象工厂，可以让我们生成相关对象的。 木门工厂可以生产木门以及木门专家，铁门工厂能够生产铁门和铁门专家。

{% highlight java %}
 interface DoorFactory
{
    public function makeDoor(): Door;
    public function makeFittingExpert(): DoorFittingExpert;
}

// Wooden factory to return carpenter and wooden door
class WoodenDoorFactory implements DoorFactory
{
    public function makeDoor(): Door
    {
        return new WoodenDoor();
    }

    public function makeFittingExpert(): DoorFittingExpert
    {
        return new Carpenter();
    }
}

// Iron door factory to get iron door and the relevant fitting expert
class IronDoorFactory implements DoorFactory
{
    public function makeDoor(): Door
    {
        return new IronDoor();
    }

    public function makeFittingExpert(): DoorFittingExpert
    {
        return new Welder();
    }
}
{% endhighlight %}

他们能够这样使用

{% highlight java %}
$woodenFactory = new WoodenDoorFactory();

$door = $woodenFactory->makeDoor();
$expert = $woodenFactory->makeFittingExpert();

$door->getDescription();  // Output: I am a wooden door
$expert->getDescription(); // Output: I can only fit wooden doors

// Same for Iron Factory
$ironFactory = new IronDoorFactory();

$door = $ironFactory->makeDoor();
$expert = $ironFactory->makeFittingExpert();

$door->getDescription();  // Output: I am an iron door
$expert->getDescription(); // Output: I can only fit iron doors

{% endhighlight %}

正如你所见的木门工厂封装了 *carpenter* 和 *wooden door* 以及铁门工厂封装了 *iron door* 和 *welder* 。 它也帮助我们确定了当创建门的时候，不会匹配到错误的专家。

**什么时候使用？** 
当依赖相关以及创建逻辑不容易的时候。

### 建造者 ###

应用例子

> 想象一下你在哈迪斯定了一份餐，说 "大哈迪斯" ，他们直接给你递过来而不用咨询你任何问题；
这是简单工厂的例子。但是这是一些包含多个步骤的创建逻辑例子。举例说，你想要一份自定义的地铁餐，你对你的汉堡包怎么做，面包片什么类型，调料什么类型，芝士想要什么等等都有自己的观点。建造者模式能够解决这样的情景。

简单地说

> 允许你创建不同口味的东东(汉堡包)从而避免构造器污染。这是非常有用的当东东(汉堡包)有几种口味的时候。或者是当东东(汉堡包)创建时候需要大量步骤的时候。

维基百科说

> 建造者模式是对象创建设计模式，主要是寻找可伸缩的构造型[反面式模式](https://en.wikipedia.org/wiki/Anti-pattern)。

让我们进一步说明一下反面式模式是什么。在某个时候我们见过构造函数像这样:

{% highlight java %}
public function __construct($size, $cheese = true, $pepperoni = true, $tomato = false, $lettuce = true)
{
}
{% endhighlight %}

就如你所见的；构造器参数很快将会无法控制以及它或许会很难去理解参数的排列等。进一步这个参数列表会继续增长如何你想要添加更多的参数。 这个我们就叫做可伸缩的构造型反面式模式。

程序例子

明智的可替代方案是使用建造者模式。 首先我们看看我们想要制作的汉堡包

{% highlight java %}
class Burger
{
    protected $size;

    protected $cheese = false;
    protected $pepperoni = false;
    protected $lettuce = false;
    protected $tomato = false;

    public function __construct(BurgerBuilder $builder)
    {
        $this->size = $builder->size;
        $this->cheese = $builder->cheese;
        $this->pepperoni = $builder->pepperoni;
        $this->lettuce = $builder->lettuce;
        $this->tomato = $builder->tomato;
    }
}
{% endhighlight %}

然后我们有建造者

{% highlight java %}

class BurgerBuilder
{
    public $size;

    public $cheese = false;
    public $pepperoni = false;
    public $lettuce = false;
    public $tomato = false;

    public function __construct(int $size)
    {
        $this->size = $size;
    }

    public function addPepperoni()
    {
        $this->pepperoni = true;
        return $this;
    }

    public function addLettuce()
    {
        $this->lettuce = true;
        return $this;
    }

    public function addCheese()
    {
        $this->cheese = true;
        return $this;
    }

    public function addTomato()
    {
        $this->tomato = true;
        return $this;
    }

    public function build(): Burger
    {
        return new Burger($this);
    }
}

{% endhighlight %}

然后我们这样使用它:

{% highlight java %}
$burger = (new BurgerBuilder(14))
                    ->addPepperoni()
                    ->addLettuce()
                    ->addTomato()
                    ->build();
{% endhighlight %}

**什么时候使用呢?**
当某个东东有着一些特点的时候，避免构重叠构造器。和工厂模式的主要区别是; 工程模式创建过程只需要一步而建造者模式创建过程通常需要好几步。


### 原型模式

应用例子

> 记得那只羊多莉不？ 那只克隆羊！ 我们不过多讨论细节重点是克隆。

简单的说

> 在存在的对象上通过克隆创建。

维基百科

> 原型模式是软件开发中的创建型设计模式。通过原型实例来创建目标的类型来使用，被克隆来产生新实例。

简单来说，它允许你创建存在对象的拷贝以及根据需要修改,并不是通过建立或者从头开始建立对象。

程序例子

在PHP中,它可以通过 *clone*来实现

{% highlight java %}
class Sheep
{
    protected $name;
    protected $category;

    public function __construct(string $name, string $category = 'Mountain Sheep')
    {
        $this->name = $name;
        $this->category = $category;
    }

    public function setName(string $name)
    {
        $this->name = $name;
    }

    public function getName()
    {
        return $this->name;
    }

    public function setCategory(string $category)
    {
        $this->category = $category;
    }

    public function getCategory()
    {
        return $this->category;
    }
}
{% endhighlight %}

它能够这样被克隆

{% highlight java %}
$original = new Sheep('Jolly');
echo $original->getName(); // Jolly
echo $original->getCategory(); // Mountain Sheep

// Clone and modify what is required
$cloned = clone $original;
$cloned->setName('Dolly');
echo $cloned->getName(); // Dolly
echo $cloned->getCategory(); // Mountain sheep

{% endhighlight %}

你也可以使用魔术方法 *_clone* 来修改克隆行为。

**什么时候使用?**
当需要创建一个类似已存在对象时候或者是创建对象相比克隆耗费太多的时候。

### 单例

实际例子

> 同一时间只允许一个国家只允许一个总统。每当值班电话响起来的时候,同样的总统不得不被起诉。总统是唯一的。

简单的说 

> 确保指定类只有一个对象被创建

维基百科说

> 在软件工程中，单例模式是用来限制类实例化单一目标的软件设计模式。这个在整个系统中仅需要一个对象来协调功能是非常有用的。 

单例模式实际上是反面模式，尽量避免过渡使用。它使用起来并不糟糕而且有些某些有效的使用例子，但是使用它我们要注意它在程序中是有着全局状态的，修改它可能会影响到其他地方，而且它很慢被调试。
另外比较不好的地方是它会导致你的代码紧耦合，同时模拟单例模式也是非常困难的。

程序例子

使用单例模式，使得构造器保持私有，不允许克隆，不允许扩展，创建一个静态变量来持有实例。

{% highlight java %}
final class President
{
    private static $instance;

    private function __construct()
    {
        // Hide the constructor
    }

    public static function getInstance(): President
    {
        if (!self::$instance) {
            self::$instance = new self();
        }

        return self::$instance;
    }

    private function __clone()
    {
        // Disable cloning
    }

    private function __wakeup()
    {
        // Disable unserialize
    }
}

{% endhighlight php %}

然后这样使用

{% highlight java %}
$president1 = President::getInstance();
$president2 = President::getInstance();

var_dump($president1 === $president2); // true

{% endhighlight php %}


### End 创建型 ###


| 日期| 作者 | 说明 |
| ------------ | ------------- | ------------ |
| 2017-03-08 | YC | 初步翻译完成 |


## Reference
[https://github.com/kamranahmedse/design-patterns-for-humans](https://github.com/kamranahmedse/design-patterns-for-humans)




