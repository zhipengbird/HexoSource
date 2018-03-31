---
title: UITest 学习笔记
date: 2017-02-27 14:38:16
tags: UITest
---

## UITest是什么？
UITest 是一个自动测试UI与交互的Testing组件
## UITest有什么作用？
它可以通过代编写代码／记录开发者的操作过程并代码化，来实现自动点击某个按钮／视图／自动输入文字等功能
## UI Test 的重要性
 在实际开发过程中，随着项目越做越大，功能越来越多，仅仅靠人功操作的方式来覆盖所有测试用修饰是非常困难的，尤其是加入新功能以后，旧的功能也要重新测试，这导致测试需要花非常多的时间来进行回规性测试。这里产生了大量的重复工作，而这些工作完全可以自动完成。这时UITest就可以帮助解决这个问题。

## XCTest 工具
 XCTest 一共提供了3种UI测试对象
 * XCUIApplication 当前测试应用target
 * XCUIElementQuery 定位查询当前UI中XCUIElement的一个类
 * XCUIElement UI测试中任何一个item项都被抽象成一个XCUIElement类型

### XCUIApplication
 XCUIApplication类是Application的代理，就像我们项目工程中的AppDelegate，这个对象用来启动或者是终止UI测试程序，还可以在启动的时候设置一些启动参数，在获取程序中的UI元素的时候，就是通过这个类的实例。这个类继承自XCUIElement类
### XCUIElement
 XCUIElement类是XCTest.framework对应用中的所有UI 控件的抽象，在UI测试中，没有UIKit中的UI类型，只是用这个类的实例表示所有的UI 控件，以及相应的交互方法，例如：执行手势（tap，press，swipe），滑动控件交互，拾取器交互，这个类采取了 XCUIElementAttributes协议（描述UI元素的属性：Identity，Value，Interaction State，Size），XCUIElementTypeQueryProvider协议(为指定类型的子代元素提供ready-made查询， 子代元素查询包含button，具体实现是:@property(readonly, copy) XCUIElementQuery *buttons; 等一系列对UIKit中元素的映射)

### XCUIElementQuery
XCUIElementQuery类是定位UI 元素的查询，这个类使用类似key-value的机制得到XCUIElement的实例，使用Type(XCUIElementType枚举)，Predicate， Identifier创建query，使用elementAtIndex:, elementMatchingPredicate,elementMatchingType: identifier:方法访问匹配到的UI元素，此类采用XCUIElementTypeQueryProvider协议

## Accessibility
为了让UI Testing能够工作，框架需要能够访问UI中的各个元素，这样才能执行它们的动作。你可以定义测试要在哪个特定的点进行点击和轻扫，但是这在不同屏幕大小的设备上就不那么好使了，又或者你换算出UI中元素在不同设备上的位置。

这时候accessibility就派上用场了。Accessibility是Apple很早之前构建的一个框架，它能帮助一些行动不便的用户来更好地使用你的应用。它为你的UI提供了丰富的语义数据，这能让不同的Accessibility功能给行动不便的用户展现你的应用。有很多功能都是现成的，直接就能在你的应用中使用，但是你可以（也应该）使用Accessibility的API来改进Accessibility关于UI的数据。在很多场景下这都是必需的，比如对一些自定义的控件，Accessibility就不清楚你的API要做什么。

UI Testing可以通过你的应用提供的Accessibility功能来与你的应用连接，这样就解决了设备大小不一的问题。如果你重新调整了UI中的某些元素，你也不用重写整套测试。实现Accessibility不仅是为了使用UI Testing，也能帮助行动不便的用户更好地使用你的应用
## UI Recording
当设置好可以访问的UI之后，你可能就想创建一些UI测试了。编写UI测试耗时又无聊，如果你的UI比较复杂，那这还有些难度。多亏在Xcode 7中，Apple引入了UI Recording，它能让你创建新的测试，并扩展原有的测试。当UI Recording打开之后，当你在真机或模拟器上与应用交互时，代码会自动生成。
