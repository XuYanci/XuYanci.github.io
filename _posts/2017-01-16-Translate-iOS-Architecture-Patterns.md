---
layout: post
title: "Translate-iOS-Architecture-Patterns"
date: 2017-01-16
excerpt: "Translate-iOS-Architecture-Patterns"
tags: [ios architecture]
comments: true
---


### iOS Architecture Patterns

#### Demystifying MVC, MVP, MVVM and VIPER

  对使用iOS中的MVC感觉到奇怪，对切换到MVVM感觉到疑惑？ 听说过VIPER,但不确定它是否名副其实？
  
  继续阅读，你将会找到以上问题的答案，如果你无法从容的解释这些问题。
  
  你将会重新构建你的iOS环境中知识架构。 我们将会简单的介绍一些通用架构，通过几个例子从理论，以及实践来比较他们。如果你需要了解更多有关他们的详情，请关注链接。
  
  掌握好设计模式或许很让人沉迷其中，注意： 你阅读完本文后，或许自己的疑问就缝纫而解，例如这些疑问：
  
  **网络请求应该放在哪里 ： 模型 或者 控制器？**
 
  **我怎样传递一个模型给新视图的视图模型呢 ？**
 
  **谁创建了VIPER 模式 ： 路由 还是 呈现 ？** 
  
  ![Mou icon](https://cdn-images-1.medium.com/max/1600/1*79nWxPpKgL7JGjYp0HrT6A.png)
  
### 为什么要关注选择一个架构
  如果你不关注的话，某一天，调试一个非常繁重的类，里面包含很多不同的东西，你将会发现自己无法去寻找和修复任何bugs。。自然地，你也无法清晰的记住这个类作为整个实体，也就是说，你将会忽略很多很重要的细节。
  如果你已经身处在这种情况下，类似这样：
  
  * 这个类是UIViewController的子类
  * 你的数据存储是直接放到UIViewController中
  * 你的UIViews几乎没做任何事情
  * 模型是个静态的数据结构
  * 你的单元测试几乎无覆盖
  
  这个会发生，尽管事实上你已经按照Apple的设计指导和完成Apple的MVC模式，但不要感到沮丧。 Apple的MVC设计模式有些问题，我们会晚点讨论它。
  
  好的架构有一些什么**特点**：
  
  1. 实体间的责任**分配**清晰明了，有着严格的角色划分。
  
  2. **可测试性**主要来自第一个特点（别担心：对于某些架构来说，可测试性是非常简单的）
  
  3. **使用简单**，维护成本低
  
  
  
#### 为什么需要分配 ####
  分配能够让我们的大脑负载均衡，当我们尝试去了解事物是如何工作的。你思考的更多，开发的更好，你的大脑将更加适应的明白当中复杂性，这样你是正确的。 但是这种能力并不是总是能够很快的线性增长以及触顶。所以最简单的方式就是实体间职责分明，这个遵守**单一责任原则**。
  
#### 为什么需要可测试 ####
 这并不是一个什么问题，对那些由于添加新特性或者重构了类导致的**错误问题**,从而使用单元测试并从中受益的人。意味着这些测试节约了开发者在运行时找到问题，从而避免在用户的机子出现以及需要花费上周的时间才能修复。

#### 为什么需要使用简单 #### 
 这个并没有什么答案可说，但是值得一提的是最好的代码就是没有写过代码. 代码越少，你的bug越少。 
 这意味着想要写更少代码不应该认为单单是因为程序员的懒惰，你选择一个简洁的方案执行的前提不应该忽略了程序的可维护性。
 
 
### MV(X) essentials
 如今我们有很多框架设计模式：
 
 * [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) 
 
 * [MVP](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter)
 
 * [MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel)
 
 * [VIPER](https://www.objc.io/issues/13-architecture/viper/)
 
 首先当中三个存放它们的实体到三个不同的类别 ：
 
 * Models - 存放业务数据或者处理数据相关的数据访问层。例如 'Person' 或者 'PersonDataProvider' 类。
 
 * Views - 存放展示层（GUI），在iOS中，前缀是UI的。
 
 * Controller/Presenter/ViewModel - 模型和视图之间的胶水或者媒介，一般来说，通过用户在视图上的事件触发改变模型，以及模型的更新反映到用户视图上。
 
 职责分离有助于我们：
 
 * 更好的了解它们 （就如我们知道的）
 
 * 重用它们 （对视图和模型更具有可操作性）
 
 * 单独的测试它们 
 
 让我们开始MV(X)模式，稍晚些时候再回到VIPER。
 

### MVC

#### 它以前是如何的
在我们讨论Apple的MVC之前，我们看看传统的[MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)。

   ![Mou icon](https://cdn-images-1.medium.com/max/1200/1*E9A5fOrSr0yVmc7Kly5C6A.png)
 
 **控制器**是**视图**和**模型**的中间媒介，所以它们并不知道双方。 控制器是最少可重用的，对我们来说也是正确的，因为我们必须有个地方存放一些并不适合放到**模型层**负责的业务逻辑。
 
 理论上来说，它看起来非常直截了当，但是你感觉到什么不对劲了不？你甚至听说用户认为MVC其实是 **Massive View Controller** 。再者，视图控制器承载东西过多也成为了iOS开发者的一个重大话题。为什么会如此呢，苹果使用传统的MVC并只是提高它一点点。	
 

### Apple's MVC

#### Reality 

 ![Mou icon](https://cdn-images-1.medium.com/max/1600/1*PkWjDU0jqGJOB972cMsrnA.png)
 
 
 Cocoa MVC鼓励用户编写 **Massive** View Controllers,因为他们包含在**视图**的生命周期，很难说他们是独立的。尽管你可以将部分业务逻辑以及数据转换放到**模型**，你并没有太多机会可以将部分工作放到**视图**，绝大部分时间，视图的职责是是发送行为给**控制器** 。 视图控制器作为回调和所有东西的数据源，同时也分发和取消网络请求等。
 
 你是不是很常遇到这样的代码：
 {% highlight swift %}
 var userCell = tableView.dequeueReusableCellWithIdentifier("identifier") as UserCell
 userCell.configureWithUser(user)
 {% endhighlight %}
 
 这个列表行，通过视图绑定模型，这样是违反MVC的原则的，但是这个经常发生，程序员们并不觉得有什么问题。如果你很严格的遵照MVC，通过控制器来配置列表行，而不是将模型传到视图中，这个将会增加控制器的代码量。
 
   > Cocoa MVC 无疑是 Massive View Controller
 
 这个问题或许不会显露出来，直到你使用**单元测试**（极有可能，你的工程中已经存在）。由于你的控制器和视图粘合的很紧，它将会难以测试，因为当你写控制器的时候，你不得不非常创意的模拟视图的创建以及生命周期等，这样你的业务逻辑尽可能与视图布局分离开来。
 
 让我们来看一个简单的演示例子：
 
 {% highlight swift %}
 import UIKit

 struct Person { // Model
   let firstName: String
   let lastName: String
 }

 class GreetingViewController : UIViewController { // View + Controller
    var person: Person!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        let greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
        self.greetingLabel.text = greeting
        
    }
    // layout code goes here
}
// Assembling of MVC
let model = Person(firstName: "David", lastName: "Blaine")
let view = GreetingViewController()
view.person = model;
 {% endhighlight %}
 
 
 > MVC 组装能够在展现的视图控制器执行
 
 这个看起来是否难以调试 ？ 我们可以将**Greeting**放到**GreetingModel**来单独测试它， 但是我们无法测试任何展示逻辑 （尽管例子上的逻辑不多）。 在**GreetingViewController**中的**UIView**相关方法（viewDidLoad,didTapButton）会加载所有的视图，这对单元测试是很不友好的。
 
 事实上，在模拟器上加载和测试 **UIViews**（e.g.iPhone4S）并不能保证它能够在其他设备上（e.g.iPad)工作得很好，因此我建议移除掉你单元测试配置中的 "Host Application"以及在真机上跑测试。
 
 > *View*和*Controller*之间的交互事实上在单元测试中不可测试的。
 
 就如同所说的那样，Cocoa MVC看起来似乎是一个非常不友好的模式。让我们对比一下它与文章开头的几个特点：
 
 * 分布 - **View** 和 **Model** 实际上是分离的，但是**View**和**Controller**却耦合太深。
 
 * 可测试性 - 因为耦合性太深，你只能测试你的**Model**。
 
 * 使用性 - 对比其他模式代码最少。 除此之外，开发者相对比较熟悉它，同时它也方便给那些经验不足的开发者维护。
 
 Cocoa MVC 是你的选择，如果你不想要花费太多时间去调研你的框架，或者是你的项目比较小，不值得花费太多的维护成本。
 
  > Cocoa MVC 就开发速度来说，是最好的架构模式。
  
  
  
### MVP

#### Cocoa MVC’s promises delivered


![Mou icon](https://cdn-images-1.medium.com/max/1600/1*hKUCPEHg6TDz6gtOlnFYwQ.png)


它看起来是不是很像Apple的MVC ? 是的，确实是，它的名称叫做[MVP](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter) (Passive View variant)。等一等。。。 这是否意味着Apple的MVC模式实际上是MVP? 
不，当然不，你回头想一想，**View**和**Controller**是紧紧耦合在一起的，而MVP的中间层，**Presenter**，并没有和视图控制器的生命周期联系，**View**可以很容易被模拟，所以在**Presenter**事实上是没有任何布局代码的，但它负责更新**View**的数据和状态。	

![Mou icon](https://cdn-images-1.medium.com/max/1600/1*mQVS34naM6TD2Fj4hU-LxA.jpeg)

> 我想告诉你的是，**UIViewController**其实是**View**
 
 就**MVP**而言，控制器子类实际上是**Views**而不是**Presenters**。区别在于测试性较强，会损耗开发速度，因为你必须要处理数据和事件绑定，接下来看以下例子：
 
  {% highlight swift %}
  import UIKit

struct Person { // Model
    let firstName: String
    let lastName: String
}

protocol GreetingView: class {
    func setGreeting(greeting: String)
}

protocol GreetingViewPresenter {
    init(view: GreetingView, person: Person)
    func showGreeting()
}

class GreetingPresenter : GreetingViewPresenter {
    unowned let view: GreetingView
    let person: Person
    required init(view: GreetingView, person: Person) {
        self.view = view
        self.person = person
    }
    func showGreeting() {
        let greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
        self.view.setGreeting(greeting)
    }
}

class GreetingViewController : UIViewController, GreetingView {
    var presenter: GreetingViewPresenter!
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self, action: "didTapButton:", forControlEvents: .TouchUpInside)
    }
    
    func didTapButton(button: UIButton) {
        self.presenter.showGreeting()
    }
    
    func setGreeting(greeting: String) {
        self.greetingLabel.text = greeting
    }
    
    // layout code goes here
}
// Assembling of MVP
let model = Person(firstName: "David", lastName: "Blaine")
let view = GreetingViewController()
let presenter = GreetingPresenter(view: view, person: model)
view.presenter = presenter
  {% endhighlight %}
 
#### 有关组合重要信息
	
  MVP是第一个展示三个独立层组合的模式问题。因为我们并不希望**View**与**Model**有什么交集，同时在展示控制器(实际上是**View**)来完成组合动作也是不合理的，我们必须在其他地方做处理。例如，我们可以创建一个应用程式**Router**服务，用来完成组装和**View-to-View**展示。这个问题	不只是在MVP模式，在**接下来的模式**也存在类似问题。
  
  让我们看看**MVP**的特征：
  
  * **分布** - 我们大部分责任都在分类 **Presenter**和**Model**，以及一个非常轻量的**View**（在上面的例子中,**Model**也是轻量的）。
  
  * **可测试性** - 非常棒，因为是视图的轻量级别，我们可以很方便的测试大部分逻辑。
  
  * **使用性** - 在我们这个不切实际且简单的例子中，代码量是MVC的双倍，同时，MVP的目的是非常清晰的。
  
 > iOS中的MVP意味着可测试性好，同时代码量大。
 
### MVP

#### With Bindings and Hooters

 
![Mou icon](https://cdn-images-1.medium.com/max/1200/1*bkB6Ho_G5De47IkJpaX5XQ.png)

 还有另外一种形式的MVP -- 管理控制器MVP。 这个变体包含**View**和**Model**直接绑定，**Presenter**仍然处理来自**View**的事件以及修改**View**。
 
 当时正如我们之前所说，责任不清晰分配是不友好的，以及**View**和**Model**的紧耦合。这个与桌面式Cocoa开发环境很类似。
 与传统的MVC一样，我并没有想到任何一个亮点能为这个糟糕的架构写个例子。
 
 
### MVVM 
#### The latest and the greatest of the MV(X) kind

[MVVM](https://en.wikipedia.org/wiki/Model_View_ViewModel)是最新的MV(X)类型，让我们看看它是否会出现MV(X)之前遇到的问题。

理论上来说 Model-View-ViewModel 看起来非常好。 **View**和**Model**我们非常熟悉，但是**Mediator**，则是以**View Model**呈现。

![Mou icon](https://cdn-images-1.medium.com/max/1600/1*uhPpTHYzTmHGrAZy8hiM7w.png)

它和MVP非常类似：

* MVVM将视图控制器认为是视图

* **View**和**Model**松耦合

除此之外，**binding**与管理版MVP类似。
然而，绑定并不是**View**和**Model**之间，而是在**View**和**View Model**之间。

因此，iOS中的**View Model**究竟是什么呢？它是**View**的依赖展示以及状态。 **View Model** 触发**Model**的改变以及通过更新模型自更新，由于我们绑定了**View**和**View Model**，最先的将会对应更新。

#### 绑定 ####
我在MVP部分简单的提到过它们，让我们在深入的讨论一下。
绑定是来自于OSX开发的工具箱，但是我们在iOS工具箱并没有看到。当然，我们有KVO以及通知，但它们并没有绑定来的方便。
因此，我们并不想去重写它们，我们有两个选择：

  * 其中一个KVO绑定的库像[RZDataBinding](https://github.com/Raizlabs/RZDataBinding)或者是[SwiftBond](https://github.com/SwiftBond/Bond)
  
  * 全面的[响应式编程](https://gist.github.com/JaviLorbada/4a7bd6129275ebefd5a6)例如 [ReactiveCocoa](https://www.google.co.uk/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0CB4QFjAAahUKEwj2l6rZv5jJAhUFUhQKHWahCKs&url=https%3A%2F%2Fgithub.com%2FReactiveCocoa%2FReactiveCocoa&usg=AFQjCNHM-pOkluiSuPsaVwVujCDTknVFUA&sig2=54zu-ATo8vDMvtXbxZYTvQ)，[RxSwift](https://github.com/ReactiveX/RxSwift/)或者是[PromiseKit](https://github.com/mxcl/PromiseKit)。
  
  事实上，在今天，当你听到"MVVM"--你将会想到ReactiveCocoa等。尽管它能够通过简单的绑定来实现MVVM，ReactiveCocoa（或者siblings）能够令你最大的使用MVVM。
  
  有些关于响应式框架的事实：能力越大，责任越大。当你使用*reactive*时候，会很容易搞混事情。也就是说，如果你做错了东西，你会花费非常多的时间来调试程序，让我们看看这个调用栈。
  
  ![Mou icon](https://cdn-images-1.medium.com/max/1600/1*WGIs3XQL1MtKiyApr-m9bg.png)
  
  在我们这个简单的例子中，FRF框架和KVO是过度使用的，事实上我们应该显示调用**View Model**来使用*showGreeting*方法更新，和使用*greetingDidChange*回调方法的一些相关属性。
  
 {% highlight swift %}
 import UIKit

struct Person { // Model
    let firstName: String
    let lastName: String
}

protocol GreetingViewModelProtocol: class {
    var greeting: String? { get }
    var greetingDidChange: ((GreetingViewModelProtocol) -> ())? { get set } // function to call when greeting did change
    init(person: Person)
    func showGreeting()
}

class GreetingViewModel : GreetingViewModelProtocol {
    let person: Person
    var greeting: String? {
        didSet {
            self.greetingDidChange?(self)
        }
    }
    var greetingDidChange: ((GreetingViewModelProtocol) -> ())?
    required init(person: Person) {
        self.person = person
    }
    func showGreeting() {
        self.greeting = "Hello" + " " + self.person.firstName + " " + self.person.lastName
    }
}

class GreetingViewController : UIViewController {
    var viewModel: GreetingViewModelProtocol! {
        didSet {
            self.viewModel.greetingDidChange = { [unowned self] viewModel in
                self.greetingLabel.text = viewModel.greeting
            }
        }
    }
    let showGreetingButton = UIButton()
    let greetingLabel = UILabel()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        self.showGreetingButton.addTarget(self.viewModel, action: "showGreeting", forControlEvents: .TouchUpInside)
    }
    // layout code goes here
}
// Assembling of MVVM
let model = Person(firstName: "David", lastName: "Blaine")
let viewModel = GreetingViewModel(person: model)
let view = GreetingViewController()
view.viewModel = viewModel
 
    
 {% endhighlight %}
 
 再次回到我们的*特征*评定：
 
 * **分布** 在我们的小例子中并不清晰，但事实上，MVVM的**View**相对于MVP的**View**有着更多的责任。因为前者是通过设置绑定，从**View Model**中来更新状态，后者是将所有事件传递给**Presenter**，并不更新自身。
 
 * **可测性** **View Model**与**View**没有任何联系，这个允许我们很方便的测试。**View**或许也是可以测试，但是因为依赖UIKit，我们往往会跳过它。
 
 * **使用性** 代码量和我们之前的MVP模式例子大致相同，但是在实际应用中，你必须要从**View**传递所有事件给**Presenter**和手动更新**View**，MVVM显得更加精简，如果使用绑定的话。
 
 > MVVM是非常吸引人的，因为它含有上述模式的优点，而且它并不需要额外的代码来更新视图，这归功于视图绑定。同时，它的可测试性仍然不错。
 
### VIPER 

(待续)




### END
 
 
 **本文翻译自** [iOSArchitecturePatterns](https://medium.com/ios-os-x-development/ios-architecture- patterns-ecba4c38de52#.67bu0gn15)

    
 