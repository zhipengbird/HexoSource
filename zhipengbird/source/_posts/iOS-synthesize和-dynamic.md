---
title: iOS @property、 @synthesize和@dynamic
date: 2016-08-23 19:23:14
tags:
- iOS

---

# `@property`
  `@Property`是声明属性的语法，它可以快速方便的为实例变量创建存取器，并允许我们通过点语法使用存取器
  > 存取器（accessor）：指用于获取和设置实例变量的方法。用于获取实例变量值的存取器是getter，用于设置实例变量值的存取器是setter。
  >用`@Property`声明一个属性之后, 编译器会自动给你生成setter和getter方法的声明以及实现还有一个以_xxx 的成员变量(xxx是你属性定义的变量名字)  

  <!-- more -->

# ` @property`对应两个选择
## `@synthesize`
* `@synthesize` 能够为您的属性的`getter`和`setter`方法。有自定义会屏蔽自动生成的`getter`和`setter`
* Xcode6以后省略这个了, 默认在 `@implementation` .m中添加了`@synthesize xxx = _xxx`;
* 如果我们希望使用默认的实例变量命名方式，那么我们在.m文件中就不需要使用`@synthesize`声明，系统会帮我们自动完成。
          如果我们希望自己命名实例变量命，那么我们就使用`@synthesize`显示声明我们希望的实例变量名。

## `@dynamic`
* `@dynamic` 不会自动生成`getter/setter`方法，需要自己实现，不然会报错
* Xcode6以后省略这个了, 默认在 `@implementation.m`中添加了`@synthesize xxx`;

# `@property的特性`
  @property还有一些关键字，它们都是有特殊作用的，它们分为三类，分别是：原子性，存取器控制，内存管理。

## 原子性
* `atomic`（默认）：`atomic`意为操作是原子的，意味着只有一个线程访问实例变量。`atomic`是线程安全的，至少在当前的存取器上是安全的。它是一个默认的特性，但是很少使用，因为比较影响效率，这跟ARM平台和内部锁机制有关。
* `nonatomic`：`nonatomic`跟`atomic`刚好相反。表示非原子的，可以被多个线程访问。它的效率比`atomic`快。但不能保证在多线程环境下的安全性，在单线程和明确只有一个线程访问的情况下广泛使用。

## 存取器控制
* `readwrite`（默认）：`readwrite`是默认值，表示该属性同时拥有`setter`和`getter`。
* `readonly`： readonly表示只有`getter`没有`setter`。
有时候为了语意更明确可能需要自定义访问器的名字：
```objc
@property (nonatomic, setter = mySetter:,getter = myGetter ) NSString *name;
```
最常见的是BOOL类型，比如标识View是否隐藏的属性hidden。可以这样声明：
```objc
@property (nonatomic,getter = isHidden ) BOOL hidden;
```


## 内存管理
> `@property`有显示的内存管理策略。这使得我们只需要看一眼`@property`声明就明白它会怎样对待传入的值

* `assign`（默认）：`assign`用于值类型，如`int`、`float`、`double`和`NSInteger`、`CGFloat`等表示单纯的复制。还包括不存在所有权关系的对象，比如常见的delegate。
* `retain`：在`setter`方法中，需要对传入的对象进行引用计数加1的操作。
  简单来说，就是对传入的对象拥有所有权，只要对该对象拥有所有权，该对象就不会被释放。如下代码所示：
  ```objc
  -(void)setName:(NSString * )_name{  
     //首先判断是否与旧对象一致，如果不一致进行赋值。  
     //因为如果是一个对象的话，进行if内的代码会造成一个极端的情况：当此name的retain为1时，使此次的set操作让实例name提前释放，而达不到赋值目的。  
     if ( name !=_name){  
          [name release];  
          name = [_name retain];  
     }  
}
  ```
* `strong`：`strong`是在`iOS`引入`ARC`的时候引入的关键字，是`retain`的一个可选的替代。表示实例变量对传入的对象要有所有权关系，即强引用。`strong`跟`retain`的意思相同并产生相同的代码，但是语意上更好更能体现对象的关系。
* `weak`：在`setter`方法中，需要对传入的对象不进行引用计数加1的操作。
  简单来说，就是对传入的对象没有所有权，当该对象引用计数为0时，即该对象被释放后，用`weak`声明的实例变量指向`nil`，即实例变量的值为0。

  >注：weak关键字是iOS5引入的，iOS5之前是不能使用该关键字的。`delegate `和 `Outlet` 一般用weak来声明。

* `copy`：与`strong`类似，但区别在于实例变量是对传入对象的副本拥有所有权，而非对象本身。
