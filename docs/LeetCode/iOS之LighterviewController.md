---
title: iOS之LighterviewController
date: 2019-05-07 23:09:00
tags: [coding]
category: [iOS编程]
---
# 被误解的 MVC 和被神化的 MVVM

# 被误解的 MVC
MVC，全称是 Model View Controller，是模型 (model)－视图 (view)－控制器 (controller) 的缩写。它表示的是一种常见的客户端软件开发框架。

<!--more-->

## Controller 的臃肿问题何解？

- 「什么样的内容才应该放到 Controller 中？」
- 我们来看看 MVC 这种架构的特点。其实设计模式很多时候是为了 Don't repeat yourself 原则来做的，**该原则要求能够复用的代码要尽量复用，来保证重用。**在 MVC 这种设计模式中，我们发现 View 和 Model 都是符合这种原则的。

对于 View 来说，你如果抽象得好，那么一个 App 的动画效果可以很方便地移植到别的 App 上，而 Github 上也有很多 UI 控件，这些控件都是在 View 层做了很好的封装设计，使得它能够方便地开源给大家复用。

对于 Model 来说，它其实是用来存储业务的数据的，如果做得好，它也可以方便地复用。比如我当时在做有道云笔记 iPad 版的时候，我们就直接和 iOS 版复用了所有的 Model 层的代码。在创业做猿题库客户端时，iOS 和 iPad 版的 Model 层代码再次被复用上了。当然，因为和业务本身的数据意义相关，Model 层的复用大多数是在一个产品内部，不太可能像 View 层那样开源给社区。

说完 View 和 Model 了，那我们想想 Controller，Controller 有多少可以复用的？我们写完了一个 Controller 之后，可以很方便地复用它吗？结论是：非常难复用。在某些场景下，我们可能可以用 addSubViewController 之类的方式复用 Controller，但它的复用场景还是非常非常少的。

如果我们能够意识到 Controller 里面的代码不便于复用，我们就能知道什么代码应该写在 Controller 里面了，那就是那些不能复用的代码。在我看来，**Controller 里面就只应该存放这些不能复用的代码**，这些代码包括：

- 在初始化时，构造相应的 View 和 Model。
- 监听 Model 层的事件，将 Model 层的数据传递到 View 层。
- 监听 View 层的事件，并且将 View 层的事件转发到 Model 层。
如果 Controller 只有以上的这些代码，那么它的逻辑将非常简单，而且也会非常短。

### 如何对 ViewController 瘦身？
objc.io 是一个非常有名的 iOS 开发博客，它上面的第一课 《Lighter View Controllers》 上就讲了很多这样的技巧，我们先总结一下它里面的观点：

- 将 UITableView 的 Data Source 分离到另外一个类中。
- 将数据获取和转换的逻辑分别到另外一个类中。
- 将拼装控件的逻辑，分离到另外一个类中。

其实 MVC 虽然只有三层，但是它并没有限制你只能有三层。所以，我们可以将 Controller 里面过于臃肿的逻辑抽取出来，形成新的可复用模块或架构层次。

### 将网络请求抽象到单独的类中

新手写代码，直接就在 Controller 里面用 AFNetworking 发一个请求，请求的完数据直接就传递给 View。入门一些的同学，知道把这些请求代码移到另外一个静态类里面。但是我觉得还不够，所以我建议将**每一个网络请求直接封装成类**。

把每一个网络请求封装成对象其实是使用了设计模式中的 Command 模式，它有以下好处：

- 将网络请求与具体的第三方库依赖隔离，方便以后更换底层的网络库。实际上我们公司的 iOS 客户端最初是基于 ASIHttpRequest 的，我们只花了两天，就很轻松地切换到了 AFNetworking。
- 方便在**基类中处理公共逻辑**，例如猿题库的数据版本号信息就统一在基类中处理。
- 方便在基类中处理缓存逻辑，以及其它一些公共逻辑。
- 方便做对象的持久化。

大家如果感兴趣，可以看我们公司开源的 iOS 网络库：YTKNetwork。它在这种思考的指导下，不但将 Controller 中的代码瘦身，而且进一步演化和加强，现在它**还支持诸如复杂网络请求管理，断点续传，插件机制，JSON 合法**性检查等功能。
这部分代码从 Controller 中剥离出来后，不但简化了 Controller 中的逻辑，也达到了网络层的代码复用的效果。

### 将界面的拼装抽象到专门的类中
新手写代码，喜欢在 Controller 中把一个个 UILabel ，UIButton，UITextField 往 self.view 上用 addSubView 方法放。我建议大家可以用两种办法把这些代码从 Controller 中剥离。

方法一：构造专门的 UIView 的子类，来负责这些控件的拼装。这是最彻底和优雅的方式，不过稍微麻烦一些的是，你需要把这些控件的事件回调先接管，再都一一暴露回 Controller。

方法二：用一个静态的 Util 类，帮助你做 UIView 的拼装工作。这种方式稍微做得不太彻底，但是比较简单。

对于一些能复用的 UI 控件，我建议用方法一。如果项目工程比较复杂，我也建议用方法一。如果项目太紧，另外相关项目的代码量也不多，可以尝试方法二。

### 构造 ViewModel
谁说 MVC 就不能用 ViewModel 的？MVVM 的优点我们一样可以借鉴。**具体做法就是将 ViewController 给 View 传递数据这个过程，抽象成构造 ViewModel 的过程**。

这样抽象之后，View 只接受 ViewModel，而 Controller 只需要传递 ViewModel 这么一行代码。而另外**构造 ViewModel 的过程，我们就可以移动到另外的类中了**。

在具体实践中，我建议大家专门**创建构造 ViewModel 工厂类**，参见 工厂模式。另外，也可以专门将数据存取都抽将到一个 Service 层，由这**层来提供 ViewModel 的获取**。

### 专门构造存储类
刚刚说到 ViewModel 的构造可以抽奖到一个 Service 层。与此相应的，数据的存储也应该由专门的对象来做。在小猿搜题项目中，我们由一个叫 UserAgent 的类，专门来处理本地数据的存取。

数据存取放在专门的类中，就可以针对存取做额外的事情了。比如：

- 对一些热点数据增加缓存
- 处理数据迁移相关的逻辑
如果要做得更细，可以把存储引擎再抽象出一层。这样你就可以方便地切换存储的底层，例如从 sqlite 切换到 key-value 的存储引擎等。

## 小结
通过代码的抽取，我们可以将原本的 MVC 设计模式中的 ViewController 进一步拆分，构造出 **网络请求层**、**ViewModel 层**、**Service 层**、**Storage 层**等其它类，来配合 Controller 工作，从而使 Controller 更加简单，我们的 App 更容易维护。

## 被神化的 MVVM

MVVM 是 Model-View-ViewModel 的简写。

MVVM 在使用当中，通常还会利用双向绑定技术，使得 Model 变化时，ViewModel 会自动更新，而 ViewModel 变化时，View 也会自动变化。所以，MVVM 模式有些时候又被称作：model-view-binder 模式。
具体在 iOS 中，可以使用 KVO 或 Notification 技术达到这种效果。

第一点：**数据绑定使得 Bug 很难被调试**。你看到界面异常了，有可能是你 View 的代码有 Bug，也可能是 Model 的代码有问题。数据绑定使得一个位置的 Bug 被快速传递到别的位置，要定位原始出问题的地方就变得不那么容易了。

第二点：对于过大的项目，**数据绑定需要花费更多的内存**。

某种意义上来说，我认为就是数据绑定使得 MVVM 变得复杂和难用了。但是，这个缺点同时也被很多人认为是优点。

## ReactiveCocoa

函数式编程（Functional Programming）和响应式编程（React Programming）也是当前很火的两个概念，它们的结合可以很方便地实现数据的绑定。

- 函数式编程（Functional Programming），函数也变成一等公民了，可以拥有和对象同样的功能，例如当成参数传递，当作返回值等。看看 Swift 语言带来的众多函数式编程的特性，就你知道这多 Cool 了。
- 响应式编程（React Programming），原来我们基于事件（Event）的处理方式都弱了，现在是基于输入（在 ReactiveCocoa 里叫 Signal）的处理方式。输入还可以通过函数式编程进行各种 Combine 或 Filter，尽显各种灵活的处理。
- 无状态（Stateless），状态是函数的魔鬼，无状态使得函数能更好地测试。
- 不可修改（Immutable），数据都是不可修改的，使得软件逻辑简单，也可以更好地测试。



而我想说，我们需要保持的是一个拥抱变化的心，以及理性分析的态度。在新技术的面前，不盲从，也不守旧，一切的决策都应该建立在认真分析的基础上，这样才能应对技术的变化。


