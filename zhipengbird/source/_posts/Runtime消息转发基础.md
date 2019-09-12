---
title: Runtime消息转发基础
date: 2019-09-09 19:59:16
tags:
- runtime
---

# Runtime 的定义

> The Objective-C language defers as many decisions as it can from compile time and link time to runtime. Whenever possible, it does things dynamically. This means that the language requires not just a compiler, but also a runtime system to execute the compiled code. The runtime system acts as a kind of operating system for the Objective-C language; it’s what makes the language work

# runtime的常见应用场景

* 面向切面编程AOP
* 方法调配method swizzing (默魔法)
* 消息转发
* 给分类添加关联属性（关联对象）
* 动态获取class和selector,属性
* KVC/KVO，修改私有属性的值

在很多文章中都能看到 IMP、SEL、selector 以及 Method 等关键字，相信大家随着对 RunTime 的逐步了解，慢慢会逐渐熟悉它们的。

>思考题： runtime 如何通过 selector 找到对应的 IMP 地址?

接下来分别说一下 IMP、SEL、selector 以及 Method.

## IMP

IMP保存的是method的地址，本质是一个函数指针，由编译器生成。

```objc
/// A pointer to the function of a method implementation.
#if !OBJC_OLD_DISPATCH_PROTOTYPES
typedef void (*IMP)(void /* id, SEL, ... */ );
#else
typedef id (*IMP)(id, SEL, ...);
#endif
```

向对象发送消息后，是由这个函数指针IMP执行的，即IMP函数指针指向了方法的实现。

IMP函数指针最少包含了id和SEL类型的两个参数，后面其他的参数是对应方法需要的参数。其中id代表执行该方法的target（对象），SEL代表对应的方法，通过id和SEL参数就能确定唯一的方法实现地址。

### 如何获取方法的IMP

NSObject提供了如下两方法：

```objc
- (IMP)methodForSelector:(SEL)aSelector;
+ (IMP)instanceMethodForSelector:(SEL)aSelector;
```

对应的实现源码：

```objc 
+ (IMP)instanceMethodForSelector:(SEL)sel {
    if (!sel) [self doesNotRecognizeSelector:sel];
    return class_getMethodImplementation(self, sel);
}
+ (IMP)methodForSelector:(SEL)sel {
    if (!sel) [self doesNotRecognizeSelector:sel];
    return object_getMethodImplementation((id)self, sel);
}
- (IMP)methodForSelector:(SEL)sel {
    if (!sel) [self doesNotRecognizeSelector:sel];
    return object_getMethodImplementation(self, sel);
}
```

上述实现中`methodForSelector`即有实例方法和类方法，而`instanceMethodForSelector`只有类方法
在使用`methodForSelector`方法时，向类发送消息，则`SEL`应该是类方法，若向实例对象发消息，则`SEL`应该为实例对象方法。`instanceMethodForSelector`仅仅允许类发送该消息，从而获取实例方法的IMP,该方法无法获取类方法的`IMP`,如果想获取类方法的`IMP`可以使用`methodForSelector`来获取。

## Method

在源码`runtime.h`中定义method,其本质是一个结构体

```objc
struct objc_method {
    SEL method_name     OBJC2_UNAVAILABLE;///函数名
    char *method_types  OBJC2_UNAVAILABLE;///方法类型
    IMP method_imp      OBJC2_UNAVAILABLE;///函数指针
}
```

* 方法名`method_name`类型为`SEL`;
* 方法类型`method_types`是一个char指针，存储着方法的参数类型和返回值类型；
* 方法实现`method_imp`的类型为`IMP`;

### 获取method的方法

`runtime.h` 中有两个方法，可以根据 `SEL`直接获取实例方法和类方法的 `Method`，如下:

```objc
Method class_getInstanceMethod(Class cls, SEL name);///获取实例方法
Method class_getClassMethod(Class cls, SEL name);///获取类方法
```

## SEL

selector称之为方法选择器，SEL是selector的表示类型，也是方法编号，是类成员方法的指针。

```objc
typedef struct objc_selector *SEL;
```

### 获取SEL有方法

```objc
SEL sel = @selector(play:);
SEL sel = sel_registerName("play:"); 
SEL sel = NSSelectorFromString(@"play");
```

SEL表示一个selector的指针，无论什么类里，只要方法名相同，SEL就相同，SEL实际是根据方法名hash化了的字符串，而对于字符串的比较仅仅需要比较他们的地址就可以了，所以速度上非常快，SEL的存在加快了查询方法的速度

> Q：为什么在同一个OC类中，不能存在同名的函数，即使用参数类型不同也不行？（OC为什么没有重载）
> A：SEL表示一个selector的指针，无论在什么类型里，只要方法名相同，SEL就相同，相同的函数名，编译器无法编译通过。

`dispatch table` 存放`SEL`和`IMP`的对应关系，`SEL`最终会通过`dispatch table`寻找到对应的`IMP`.

**Selector,Method,IMP三者的关系**
在类的（实例和类方法）调度表(dispatch table)中每一个实体代表一个方法的Method，其名叫选择器SEL，并对应着一种方法实现称之为IMP，有了Method就可以使用SEL找到对应的IMP,SEL就是为了查找方法的最终实现IMP


## class_addMethod

```objc
BOOL class_addMethod(Class cls, SEL name, IMP imp, const char *types)
{
    if (!cls) return NO;
    rwlock_writer_t lock(runtimeLock);
    return ! addMethod(cls, name, imp, types ?: "", NO);
}
```

> Adds a new method to a class with a given name and implementation.
class_addMethod will add an override of a superclass's implementation,
>but will not replace an existing implementation in this class.
To change an existing implementation, use method_setImplementation.

方法参数解析：

* 返回值: 返回 YES 表示方法添加成功, 否则添加失败
* 参数 Class cls: 将要给添加方法的类, 即［类名 class］
* 参数 SEL name: 将要添加的方法 SEL, 即 @selector(方法名)，如果已经存在，该方法返回失败，不存在就添加成功。
* 参数 IMP imp：实现这个方法的函数. 有两种写法即 C 和 OC 的写法. 一个 IMP 最少包括两个参数, 上面已经说过。
* 参数 const char *types: 实现方法的函数的返回和参数编码类型. 如 "v@:" 表示返回值为 void, 没有参数的一个函数, 其中 @和:分别代表 IMP 的默认两个参数即 id 和 sel.


> **思考题解答**
> `runtime 如何通过 selector 找到对应的 IMP 地址?`
> A:类对象中有类方法和实例方法的分发表，表中记录着方法的名字、参数和实现，selector本质就是方法名，runtime通过这个方法名称可以在分发表中找到方法的对应实现。
> 系统为我们提供了获取IMP指针的函数，无论是类方法和实例方法都可以获取对应的IMP。而Method将selector和IMP联系起来，IMP是函数指针，由编译器编译生成，当发一个消息时，它会找到那段代码执行，IMP指向了这个方法的具体实现，得到这个函数的指针就可以运行了。
> IMP指向的方法与objc_msgSend函数类型相同，参数都包含id和SEL类型。每个方法名都对应一个SEL类型的方法选择器，而每个实例对象中的SEL对应的方法实现肯定是唯一的，通过一组id和SEL参数就能确定唯一的方法实现地址，反之为亦然。当发送消息给一个对象时，runtime会在对象的类对象方法列表里找，当我们发送一个消息给一个类时，这条消息会在类的metaClass对象的方法列表里查找，直到在NSObject为止。
