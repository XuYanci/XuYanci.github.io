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

待续


