---
layout: post
title: "Translate-iOS-DesignPatterns(2)"
date: 2017-03-19
excerpt: "Translate-iOS-DesignPatterns(2)"
tags: [designpatterns]
comments: true
---

### 结构型设计模式 ###

简单的说
> 结构型模式主要是关注对象组合，换句话说实体之间如何互相使用。另外一个解释是，它能够帮助回答 "怎样建立一个软件零件"

维基百科说

> 在软件工程中，结构型设计模式是通过识别一个简单的方式来实现实体之间的联系。

* [适配](https://github.com/XuYanci/design-patterns-for-humans#-adapter)
* [桥接](https://github.com/XuYanci/design-patterns-for-humans#-bridge)
* [组合](https://github.com/XuYanci/design-patterns-for-humans#-composite)
* [装饰者](https://github.com/XuYanci/design-patterns-for-humans#-decorator)
* [外观](https://github.com/XuYanci/design-patterns-for-humans#-facade)
* [享元](https://github.com/XuYanci/design-patterns-for-humans#-flyweight)
* [代理](https://github.com/XuYanci/design-patterns-for-humans#-proxy)


#### 适配器 #### 

应用例子

> 考虑一下你的内存卡里面有一些图片，你需要将它们传送到你的电脑中。要传输它们的话，你需要某种类型的适配器兼容你电脑端口，这样你可以连接你的内存卡到你的电脑。在这个例子中读卡器就是适配器。另外一个例子就是那个有名的电源适配器；三脚插头没法连接到两孔的插座。它需要使用电源适配器来让它兼容两孔的插座。还有另外一个例子是翻译一个人说的话给另外一个人。

简单的说

> 适配者模式让你包装一个不兼容的对象在适配器中，让它与其他类兼容。

维基百科说

> 在软件工程中，适配器模式是软件设计模式，允许一个已经存在类的接口能够以另外一个接口来使用。它经常用来使用存在的类来与其他类工作而不用修改他们的源代码。

#### Programmatic Example ####

考虑一下有猎人角色以及被捕猎的狮子角色的游戏。
首先我们有一个**狮子**接口由其他狮子类型的接口来实现。

{% highlight java %} 
interface Lion
{
    public function roar();
}

class AfricanLion implements Lion
{
    public function roar()
    {
    }
}

class AsianLion implements Lion
{
    public function roar()
    {
    }
}
{% endhighlight %}

猎人hunt函数需引用狮子实现接口。

{% highlight java %}
class Hunter
{
    public function hunt(Lion $lion)
    {
    }
}
{% endhighlight %}


现在我们不打不添加一个**WildDog**在我们的游戏中，这样猎人也就可以捕猎它。但是我们不能直接处理，因为狗狗有着另外一个不同接口。让它兼容我们的猎人的话，我们要创建一个适配器来兼容。

{% highlight java %}
// This needs to be added to the game
class WildDog
{
    public function bark()
    {
    }
}

// Adapter around wild dog to make it compatible with our game
class WildDogAdapter implements Lion
{
    protected $dog;

    public function __construct(WildDog $dog)
    {
        $this->dog = $dog;
    }

    public function roar()
    {
        $this->dog->bark();
    }
}
{% endhighlight %}

现在**WildDog**可以在在我们的游戏中使用**WildDogAdapter**。

{% highlight java %}
$wildDog = new WildDog();
$wildDogAdapter = new WildDogAdapter($wildDog);

$hunter = new Hunter();
$hunter->hunt($wildDogAdapter);
{% endhighlight %}


### 桥接 ###

应用例子

> 考虑一下你有一个不同页面的网站，你能够允许用户修改主题。你应该做些什么？ 为每一个主题创建多个页面的拷贝或者是创建单独的主题以及根据用户配置加载他们？ 桥接模式允许你做第二种模式。

![Mou icon](https://cloud.githubusercontent.com/assets/11269635/23065293/33b7aea0-f515-11e6-983f-98823c9845ee.png)

简单的说

>  桥接模式主要倾向于组合而不是继承。细节完成部分由一个层次推向到另外一个单独层次的目标。

维基百科说

> 桥接模式是设计模式用在软件工程中，从他的实现解耦出抽象部分实现单独变化。

程序例子

我们将上面那个网页例子转换一下。我们现在有**WebPage**层次

{% highlight java %}
interface WebPage
{
    public function __construct(Theme $theme);
    public function getContent();
}

class About implements WebPage
{
    protected $theme;

    public function __construct(Theme $theme)
    {
        $this->theme = $theme;
    }

    public function getContent()
    {
        return "About page in " . $this->theme->getColor();
    }
}

class Careers implements WebPage
{
    protected $theme;

    public function __construct(Theme $theme)
    {
        $this->theme = $theme;
    }

    public function getContent()
    {
        return "Careers page in " . $this->theme->getColor();
    }
}
{% endhighlight %}

分离主题层次

{% highlight java %}

interface Theme
{
    public function getColor();
}

class DarkTheme implements Theme
{
    public function getColor()
    {
        return 'Dark Black';
    }
}
class LightTheme implements Theme
{
    public function getColor()
    {
        return 'Off white';
    }
}
class AquaTheme implements Theme
{
    public function getColor()
    {
        return 'Light blue';
    }
}
{% endhighlight %}

所有的层次

{% highlight java %}
$darkTheme = new DarkTheme();

$about = new About($darkTheme);
$careers = new Careers($darkTheme);

echo $about->getContent(); // "About page in Dark Black";
echo $careers->getContent(); // "Careers page in Dark Black";

{% endhighlight %}

### 组合模式 

应用例子

> 每一个公司都由员工组成。 每一个员工都有着同样的特性,例如 有工资，有责任，或许需要向某人报告，或许有下属等。 

简单的说

> 组合模式让你用同一种方式对待不同的对象。

维基百科说

> 在软件工程中，组合模式是的分隔的设计模式。组合模式描述一组对象可以作为单一对象来相同对待。组合的目的是组合对象到树结构中来代表部分-全部分类。完成组合模式让你同样的方式对待不同的目标以及组合。

程序例子

我们看看上面的雇员例子. 我们有着不同的雇员类型

{% highlight java  %}
interface Employee
{
    public function __construct(string $name, float $salary);
    public function getName(): string;
    public function setSalary(float $salary);
    public function getSalary(): float;
    public function getRoles(): array;
}

class Developer implements Employee
{
    protected $salary;
    protected $name;

    public function __construct(string $name, float $salary)
    {
        $this->name = $name;
        $this->salary = $salary;
    }

    public function getName(): string
    {
        return $this->name;
    }

    public function setSalary(float $salary)
    {
        $this->salary = $salary;
    }

    public function getSalary(): float
    {
        return $this->salary;
    }

    public function getRoles(): array
    {
        return $this->roles;
    }
}

class Designer implements Employee
{
    protected $salary;
    protected $name;

    public function __construct(string $name, float $salary)
    {
        $this->name = $name;
        $this->salary = $salary;
    }

    public function getName(): string
    {
        return $this->name;
    }

    public function setSalary(float $salary)
    {
        $this->salary = $salary;
    }

    public function getSalary(): float
    {
        return $this->salary;
    }

    public function getRoles(): array
    {
        return $this->roles;
    }
}
{% endhighlight %}

我们有个组织包含不同类型的雇员

{% highlight java %}

class Organization
{
    protected $employees;

    public function addEmployee(Employee $employee)
    {
        $this->employees[] = $employee;
    }

    public function getNetSalaries(): float
    {
        $netSalary = 0;

        foreach ($this->employees as $employee) {
            $netSalary += $employee->getSalary();
        }

        return $netSalary;
    }
}
And then it can be used as

// Prepare the employees
$john = new Developer('John Doe', 12000);
$jane = new Designer('Jane', 10000);

// Add them to organization
$organization = new Organization();
$organization->addEmployee($john);
$organization->addEmployee($jane);

echo "Net salaries: " . $organization->getNetSalaries(); // Net Salaries: 22000

{% endhighlight java %}  

### 装饰者

应用例子

> 想象你运营一个提供多种服务的汽车服务商店。现在你怎样计算账单？ 你挑选一个服务以及动态添加到提供服务的价格中直到你获取到最终花费。这里每种类型的服务器都是装饰者。

简单的说

>  装饰者模式让你运行中动态修改对象的行为，通过封装成一个装饰者类。

维基百科说 

> 在面向对象编程中,装饰者模式是一种设计模式,允许行为动态添加到单独的目标中,要么静态或者动态的,在同一个类中不需要影响其他目标的行为。装饰者模式由于遵循着单一职责原则所以非常有用的,它允许功能根据不同关注领域分类。

程序例子 

让我们以咖啡这个为例子。 首先我们有个实现咖啡接口的简单咖啡

{% highlight java %}
interface Coffee
{
    public function getCost();
    public function getDescription();
}

class SimpleCoffee implements Coffee
{
    public function getCost()
    {
        return 10;
    }

    public function getDescription()
    {
        return 'Simple coffee';
    }
}
{% endhighlight java %}

我们想要使得代码可扩展从而允许选项可变更。 让我们做些扩展 (装饰者)


{% highlight java %}
class MilkCoffee implements Coffee
{
    protected $coffee;

    public function __construct(Coffee $coffee)
    {
        $this->coffee = $coffee;
    }

    public function getCost()
    {
        return $this->coffee->getCost() + 2;
    }

    public function getDescription()
    {
        return $this->coffee->getDescription() . ', milk';
    }
}

class WhipCoffee implements Coffee
{
    protected $coffee;

    public function __construct(Coffee $coffee)
    {
        $this->coffee = $coffee;
    }

    public function getCost()
    {
        return $this->coffee->getCost() + 5;
    }

    public function getDescription()
    {
        return $this->coffee->getDescription() . ', whip';
    }
}

class VanillaCoffee implements Coffee
{
    protected $coffee;

    public function __construct(Coffee $coffee)
    {
        $this->coffee = $coffee;
    }

    public function getCost()
    {
        return $this->coffee->getCost() + 3;
    }

    public function getDescription()
    {
        return $this->coffee->getDescription() . ', vanilla';
    }
}
{% endhighlight java  %}

让我们现在制作一杯咖啡

{% highlight java  %}
$someCoffee = new SimpleCoffee();
echo $someCoffee->getCost(); // 10
echo $someCoffee->getDescription(); // Simple Coffee

$someCoffee = new MilkCoffee($someCoffee);
echo $someCoffee->getCost(); // 12
echo $someCoffee->getDescription(); // Simple Coffee, milk

$someCoffee = new WhipCoffee($someCoffee);
echo $someCoffee->getCost(); // 17
echo $someCoffee->getDescription(); // Simple Coffee, milk, whip

$someCoffee = new VanillaCoffee($someCoffee);
echo $someCoffee->getCost(); // 20
echo $someCoffee->getDescription(); // Simple Coffee, milk, whip, vanilla
{% endhighlight java  %}


### 外观模式

应用例子

> 你怎么打开电脑? "按下电源按钮" 你这么说！ 这是因为你相信因为你在使用一个电脑暴露在外面的接口，内部它不得不做大量东西。这个简单接口对于复杂子系统来说就是外观。

简单的说 

> 外观模式给复杂子系统提供一个简化的接口。

维基百科说

> 外观是一个给大量代码实体提供一个简单接口，例如类库。

程序里子

让我们看看上面的电脑例子. 这里我们有电脑类

{% highlight java %}

class Computer
{
    public function getElectricShock()
    {
        echo "Ouch!";
    }

    public function makeSound()
    {
        echo "Beep beep!";
    }

    public function showLoadingScreen()
    {
        echo "Loading..";
    }

    public function bam()
    {
        echo "Ready to be used!";
    }

    public function closeEverything()
    {
        echo "Bup bup bup buzzzz!";
    }

    public function sooth()
    {
        echo "Zzzzz";
    }

    public function pullCurrent()
    {
        echo "Haaah!";
    }
}
{% endhighlight %}

现在我们有一个外观


{% highlight java  %}
class ComputerFacade
{
    protected $computer;

    public function __construct(Computer $computer)
    {
        $this->computer = $computer;
    }

    public function turnOn()
    {
        $this->computer->getElectricShock();
        $this->computer->makeSound();
        $this->computer->showLoadingScreen();
        $this->computer->bam();
    }

    public function turnOff()
    {
        $this->computer->closeEverything();
        $this->computer->pullCurrent();
        $this->computer->sooth();
    }
}
{% endhighlight   %}


让我们使用这个外观

{% highlight java  %}
$computer = new ComputerFacade(new Computer());
$computer->turnOn(); // Ouch! Beep beep! Loading.. Ready to be used!
$computer->turnOff(); // Bup bup buzzz! Haah! Zzzzz
{% endhighlight   %}


### 享元模式

应用例子

> 你是不是从小摊买过新鲜茶叶? 他们经常售卖几杯你所需要的，剩下的就保留起来给其他客人用。 享元模式就是有关这些.分享。

简单的说

> 他是用来最小化内存使用或者是计算花费通过最大化的共享类似的目标。

维基百科说

> 在电脑编程中，享元模式是一个软件设计模式。享元模式是能够用来最小化内存使用或者是计算花费通过最大化的共享类似的目标。它是如何正确大量对象的一种方式，当简单重复出现有可能会导致使用无法预估的内存。

程序例子

转换我们上面的茶例子.首先我们有茶类型和茶制作者

{% highlight java %}
// Anything that will be cached is flyweight.
// Types of tea here will be flyweights.
class KarakTea
{
}

// Acts as a factory and saves the tea
class TeaMaker
{
    protected $availableTea = [];

    public function make($preference)
    {
        if (empty($this->availableTea[$preference])) {
            $this->availableTea[$preference] = new KarakTea();
        }

        return $this->availableTea[$preference];
    }
}
{% endhighlight java  %}


然后我们有接受订单和服务**茶店**

{% highlight java  %}
class TeaShop
{
    protected $orders;
    protected $teaMaker;

    public function __construct(TeaMaker $teaMaker)
    {
        $this->teaMaker = $teaMaker;
    }

    public function takeOrder(string $teaType, int $table)
    {
        $this->orders[$table] = $this->teaMaker->make($teaType);
    }

    public function serve()
    {
        foreach ($this->orders as $table => $tea) {
            echo "Serving tea to table# " . $table;
        }
    }
}
{% endhighlight   %}


它可以这么被使用

{% highlight java %}
$teaMaker = new TeaMaker();
$shop = new TeaShop($teaMaker);

$shop->takeOrder('less sugar', 1);
$shop->takeOrder('more milk', 2);
$shop->takeOrder('without sugar', 5);

$shop->serve();
// Serving tea to table# 1
// Serving tea to table# 2
// Serving tea to table# 5
{% endhighlight   %}

### 代理模式

应用例子

> 你是否曾使用过门禁卡开门呢? 有几种方式可以打开门. 它可以使用门禁卡打开或者是通过绕过安全按下一个按钮。 门的主要功能是被打开但是在它的上面通过代理添加某些功能。让我们使用代码里子来更好的解释它们。

简单地说

> 使用代理模式，用来代表另外一个类的功能的类

维基百科说

>  代理，在它最通用的形式，是一个功能类作为其它东西的接口。代理是封装或者是代理对象，通过客户端调用来访问场景后面的真实服务目标。代理的使用能够最终访问到真实目标，或者提供额外的逻辑功能。代理中能够提供额外的功能，例如当操作在资源密集型的真实对象能够缓存，或者在触发真实对象操作前检查条件。

程序例子

以我们的安全门为例。首先我们拥有门接口以及门实现

{% highlight java %}
interface Door
{
    public function open();
    public function close();
}

class LabDoor implements Door
{
    public function open()
    {
        echo "Opening lab door";
    }

    public function close()
    {
        echo "Closing the lab door";
    }
}
{% endhighlight java  %}

然后我们有着能提高任何门安全性的代理

{% highlight java %}
class Security
{
    protected $door;

    public function __construct(Door $door)
    {
        $this->door = $door;
    }

    public function open($password)
    {
        if ($this->authenticate($password)) {
            $this->door->open();
        } else {
            echo "Big no! It ain't possible.";
        }
    }

    public function authenticate($password)
    {
        return $password === '$ecr@t';
    }

    public function close()
    {
        $this->door->close();
    }
}
{% endhighlight java %}


如何使用它

{% highlight java %}
$door = new Security(new LabDoor());
$door->open('invalid'); // Big no! It ain't possible.

$door->open('$ecr@t'); // Opening lab door
$door->close(); // Closing lab door

{% endhighlight java %}


还有另外一个例子是某种数据映射实现。 例如，我现在使用这个模式写了一个MongoDB的对象数据映射层，我写了一个代理封装了mongo的类，当使用魔术方法 *_call()*， 所有的方法调用都代理到原生的mongo类以及返回它所期望的结果。不过 *find* 或者 *findOne* 数据映射到所需的类目标以及目标返回并不是*Cursor*。

### End 结构型 ###


| 日期| 作者 | 说明 |
| ------------ | ------------- | ------------ |
| 2017-03-28 | YC | 初步翻译完成 |



### Reference

[https://github.com/kamranahmedse/design-patterns-for-humans](https://github.com/kamranahmedse/design-patterns-for-humans)




