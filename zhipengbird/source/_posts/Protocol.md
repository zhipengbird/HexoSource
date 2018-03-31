---
title: Protocol的使用及相关事项
date: 2017-06-29 11:34:46  
categories: 
  - iOS   
tags: 
 - Protocol
---
在iOS开发中，Protocol是一种经常用到的设计模式，苹果的系统框架中也普遍用到了这种方式，比如UITableView中的<UITableViewDelegate>，以及<NSCopying>、<NSObject>这样的协议。我想大家也都自定义过协议，一般都用于回调，或者数据传递. 接下来我们来了解下协议使用及相关事项。
# 什么是协议
苹果官方给出如下的定义
> A protocol declares a programmatic interface that any class may choose to implement. Protocols make it possible for two classes distantly related by inheritance to communicate with each other to accomplish a certain goal. They thus offer an alternative to subclassing. Any class that can provide behavior useful to other classes may declare a programmatic interface for vending that behavior anonymously. Any other class may choose to adopt the protocol and implement one or more of its methods, thereby making use of the behavior. The class that declares a protocol is expected to call the methods in the protocol if they are implemented by the protocol adopter.
   {% asset_img protocol_2x.png  Protocol %}

* 正式协议与非正式协议
> There are two varieties of protocol, formal and informal:
>* A formal protocol declares a list of methods that client classes are expected to implement. Formal protocols have their own declaration, adoption, and type-checking syntax. You can designate methods whose implementation is required or optional with the @required and @optional keywords. Subclasses inherit formal protocols adopted by their ancestors. A formal protocol can also adopt other protocols.
Formal protocols are an extension to the Objective-C language.
>* An informal protocol is a category on NSObject, which implicitly makes almost all objects adopters of the protocol. (A category is a language feature that enables you to add methods to a class without subclassing it.) Implementation of the methods in an informal protocol is optional. Before invoking a method, the calling object checks to see whether the target object implements it. Until optional protocol methods were introduced in Objective-C 2.0, informal protocols were essential to the way Foundation and AppKit classes implemented delegation.


# 协议的定义
1. 协方式的定义
```objc 
@protocol Protocolname <NSObject>
@required
//一系列必选方法
@optional
//一系列可选方法

//属性的声明
@end
```
2. 协议里可以定义哪些东西：
  >Protocols can include declarations for both instance methods and class methods, as well as properties.
  
 协议可以定义实例方法，类方法以及属性  

3. 协议的继承  
>In the same way that an Objective-C class can inherit from a superclass, you can also specify that one protocol conforms to another.  
It’s best practice to define your protocols to conform to the **NSObject** protocol (some of the **NSObject** behavior is split from its class interface into a separate protocol; the **NSObject** class adopts the **NSObject** protocol)

    协议默认继承 ***< NSObject >*** 协议 ,你也可以指定一个其他协议进行继承

4. 协议中定义属性
    协议里是支持定义属性的，但这只是定义了getter和setter方法，并没有具体的实现。所以当协议属性修饰符为@required时，如果遵守协议的类不实现这两方法，编译器会报出警告。最简单的方式是加上属性同步语句@synthesize propertyName;
  

# 协议的关键字
1. @required
> By default, all methods declared in a protocol are required methods. This means that any class that conforms to the protocol must implement those methods.

    @required修改的协议方法为必要方法，任何遵守该协议的类都必须实现这些方法

2. @optional
> It’s also possible to specify optional methods in a protocol. These are methods that a class can implement only if it needs to. 

    @optional修饰的方法为可选方法，这些方法可以根据类的需要进行实现。


# 遵守协议
1. 设置代理属性
    ```objc
    @interface Object : NSObject<Protocolname>
    @property(nonatomic,weak)id<Protocolname>delegate;
    ...
    @end
    ```
    * <协议列表>
        若需要遵守多个协议，将协议用逗号隔开，并放在尖括号<>中 
    * 添加代理属性
        代理属性通常用**weak**修饰，避免强引用
2. 实现协议方法
    * @required修饰的协议方法，需要在.m文件中对这些方法进行实现 
    * @optional修饰的协议方法，可根据需要在.m文件中进行实现    


# 检测对象是否遵守协议
1. 检测某对象是否遵守该协议 
使用 `- (BOOL)conformsToProtocol:(Protocol *)aProtocol;`进行检测
```objc
[delegate conformsToProtocol:@protocol(Protocolname)];
```
2. 检测对象是否实现了某方法 
使用 `- (BOOL)respondsToSelector:(SEL)aSelector;`进行检测
```objc
[delegate respondsToSelector:@selector(methodname)];
```


