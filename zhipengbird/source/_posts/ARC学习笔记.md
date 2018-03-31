---
title: ARC学习笔记
date: 2018-02-04 22:32:07
tags:
- ARC 
---
# ARC
ARC代表自动引用计数（Automatic Reference Counting）,它是在开发Clang Anlyzer时所获得的经验的基础上构建的，是检查代码并在任何需要的地方插入retain,release和autorelease消息的编译过程的一部分。
# ARC是什么，不是什么？
ARC的一些基本要点：
 * ARC中的`RC`代表引用计数，ARC不是一种全新的和不同的内存管理模式，它可以使用现有的OC引用计数系统自动化
 * ARC为OC对象管理内存。它不会管理CF对象（它们具有自己的引用计数系统）或者malloc分配的原始字节。如查直接利用`malloc()`分配的一些字节，就必须记得利用`free()`释放它们。如果创建了一些CF对象，也必须记得释放它们
 * ARC的工作是作为编译过程的一部分执行的。不过，它需要一个实现了一组指定函数的运行库。
 * ARC可以与利用手动引用计数编译的文件和库互操作。 在编译一个项目时，可以在逐个文件上基础上打开和关闭ARC
 * ARC不会查找或校正保留循环。新的弱引用系统提供了一个工具，有助于阻止保留循环。
 * ARC可以在iOS和OS X上工作，但是在OSX上，ARC只能与64位新的运行库协同工作。

# ARC的规则
 * 不能实现或调用retain,release,autorelease或者retainCount方法.这一限制不仅针对对象，对选择器同样有效。因此[obj relase],@selector(retain)会在编译时报错。
 * 可以实现dealloc方法，但不能调用它们。不仅不能调用其他对象的dealloc,也不能调用超类的。[super dealloc]是编译时的错误。 但仍然可以对Core foundation类型的对象调用CFRetain,CFRelease 等相关方法。
 * 不能调用NSAllocateObject和NSDeallocObject方法。应使用alloc方法创建对象，运行时负责回收对象。
 * 不能在C语言结构体内使用对象指针
 * 不能在id类型和void*类型之间进行自动转换。 如果需要，必须显示转换
 * 不能使用NSAutoreleasPool，要使用autoreleasepool块替换。
 * 不能使用NSZone内存区域
 * 属性的访问器名称不能以`new`开头，确保与MRC的互操作性，
 
## ARC 和dealloc
在手动引用计数下，dealloc方法的主要任务是***释放保存在保留的实例变量中的任何对象。你的对象将会消失，并且dealloc是你不得不交出在你的对象的实例变量中保存的任何对象的所有权的最后机会***
在ARC下，将负责释放保存在实现变量中的任何对象，不再需要调用`[super dealloc]`,编译器将会为你自动处理。
> ARC把释放保存在类的实例变量中的对象作为它在销毁对象时所做的最后几个事情之一。此时，将会调用你的dealloc方法，所有保存在实例变量中的对象仍然是活动和有效的，即使用只有你的对象保存了指向它们的强引用也是如此。

## 方法命名约定
手动引用计数的约定：
   * 其名称以“alloc”,"new"，“cpy”,"mutablecopy"开头的方法将返回具有“+1的保留计数”的对象。你拥有通过这样的方法返回的任何对象
   * 对象是由其他任何类型的名称的方法返回的，则不拥有它们。
ARC将该规则转变成了一个官方规则：
* 以“alloc”，“new”，“copy”，“mutableCopy”开头的方法必须把所有权传递给调用代码，具有其他名称的方法则不会传递所有权
如果希望在方法中以这些单词开头，可以通过向方法声明中添加`RETURN_NOT_RETAIN`或`RETURN_RETAIN`来绕开这些规则
 
## ARC 需要看到方法声明
ARC需要看到它在消息表达式中遇到的每个方法的声明，“看到”指方法在与消息表达式相同的文件中或者是在最终会导入的文件中或者最终会导入那个文件的某个文件声明中。
在手动引用计数下：
```
Foo  * foo = [[Foo alloc]init];
[foo  undeclareMethod];
```
会得到一个警告：`Instance method '-undeclareMethod' not found(return type defaults to 'id')`
使用ARC，警告将会变成错误：
`No visible @interface for 'Foo' declares the selector 'undeclaredMethod'`

这是由于ARC不能通过方法名确定方法将会返回一个对象还是一种基本数据类型。尽管有这些规则，ARC还是不能只通过查看方法名来判断返回对象的内存状态是什么以。可以通过向方法声明中添加`NS_RETURNS_NOT_RETAINED`或`NS_RETURNS_RETAINED`,改变方法期望的保留状态。
## OC 指针与C结构
不能把指向OC对象的指针放在C结构（struce）或联合（union）中，ARC将不允许编写下面代码：
```
  struct _sentence{
        NSString * noun;
        NSString * verb
    }sentence;
    //    ARC forbids Objective-C objects in struct
```
ARC无法可靠地遵循C结构的生存期，因此，它无法知道自己何时可以安全的释放赋予结构成员的任何对象，通过把结构体的成员声明为`__unsafe_unretained`就可以解决。
```
    struct _sentence{
        __unsafe_unretained NSString * noun;
        __unsafe_unretained NSString * verb
    }sentence;
```
`__unsafe_unretained`是ARC引入的变量修饰符，当把一个变量声明为`__unsafe_unretained`时，它告诉ARC把在一个对象赋予该变量时放弃进行任何内存管理。
更好的解决方案是把结构变成一个类：
```
@interface Sentence: NSObject
@property (nonatomic) NSString * noun;
@property (nonatomic) NSString * verb;
@end
```
# 引用类型
ARC带来了新的引用类型：弱引用。其支持的引用类型有：强引用、弱引用
* 强引用
    强引用是默认的引用类型。被强引用指向的内存不会被释放，强引用会对引用计数加1，从而扩展对象的生命周期。
* 弱引用
    弱引用是一种特殊的引用类型。它不会增加引用计数，因而不会扩展对象的生命周期。在启用ARC的开发中，弱引用显得格外重要。

# 变量修饰符
## `__strong`
当把对象存储在一个`__strong`变量中时，就会创建一个指向该对象的强引用。强引用将使用对象保持活动状态：
当ARC在一个`__strong`变量中存储对象时，它将给该对象发送一条`retain`消息。如果变量已经保存有一个对象，ARC将给当前的对象发送一条`release`或`autorelease`消息。 当保存有一个指向对象的引用的`__strong`变量超出作用域时，该对象也会接收到一个`release`或`autorelease`消息。  
当最后一个指向对象的强引用消失时，对象的保留引用计数归零，对象将取消分配，并且会把它的字节归还给堆。
栈局部变量以及保存对象的函数和方法的实参默认也是`__strong`类型.在转换没有使用ARC的OC程序时，将把保存对象的栈局部变量初始化为nil.  
由于实例变量也会初始化为nil,这意味着每个OC对象的强变量要么是nil,要么保存一个对象，该对象在存储到变量时将会被保留。这是一个重要的保证：**每个强类型变量要么包含一个有效的对象，要么是nil,。没有悬挂指针，这样就可以对一大类错误说goodbye**

## `__weak`
这表明引用是不会保持被引用对象的存活。当没有强引用指向对象时，弱引用会被置为nil,可以将`__weak`看作assign操作符的ARC版本，只是对象被回收时，`__weak`具有安全性，指针将自动被设置为nil.

## `__autoreleasing`
`__autoreleasing`用于由引用使用`id *`传递的消息参数，它预期了`autorelease`方法会在传递参数中调用。

## `__unsafe_unretained`
与`__weak`类似，只是当没有强引用指向对象时，`__unsafe_unretained`不会被置为nil,可将其看作assin操作符的ARC版本。
> 使用这些限制符的方法  
`typename * qualify(限制符) variable（变量）;`

# 属性限定符
* strong  默认修饰符，指定了`__strong`关系
* weak  指定了`__weak`关系
* assign 在MRC下,assign 是默认的持有关系限制符，在ARC下表示`__unsafe_unretained`关系
* copy  暗指了`__strong`关系
* retain 指定了`__strong`关系
* unsafe_unretained 指定了`__unsafe_unretained`关系

> `assign`和`unsafe_unretained`只进行值复制而没有任何的实质检查，所以它们只用于值类型（BOOL,NSInteger,...），应避免将它们使用于引用类型，尤其是指针类型（NSString*,...）

# 最佳实践
* 避免使用大量的单例
* 对子对象使用`__strong`
* 对父对象使用`__weak`
* 对使用引用图闭合的对象(如代理)使用`__weak`
* 对数值属性（NSInteger,SEL,CGFloat，...）使用`assign`
* 对块属性，使用`copy`
* 当声明使用`NSError **`参数的方法时，需要使用`__autoreleasing`，并注意正确的语法：`NSError * __autoreleasing*`
* 避免在块内直接引用外问的变量
* 进行必要的清理时，遵守以下准则：
 * 销毁计时器
 * 移除观察者(移除对通知的注册)
 * 解除回调（将强引用的委托设置为nil）


内存使用情况获取
```c 
#import <mach/mach.h>
/**获取使用内存大小*/
vm_size_t getUsedMemory(){
    task_basic_info_data_t info ;
    mach_msg_type_number_t size = sizeof(info);
    kern_return_t kerr = task_info(mach_task_self(), TASK_BASIC_INFO, (task_info_t)&info, &size);
    if (kerr == KERN_SUCCESS) {
        return info.resident_size;
    }else{
        return 0;
    }
}
/**获取空余内存大小*/
vm_size_t getFreeMemory(){
    mach_port_t host  = mach_host_self();
    mach_msg_type_number_t size = sizeof(vm_statistics_t)/sizeof(integer_t);
    
    vm_size_t pagesize;
    vm_statistics_data_t vmstat;
    host_page_size(host, &pagesize);
    host_statistics(host, HOST_VM_INFO, (host_info_t)&vmstat, &size);
    
    return vmstat.free_count * pagesize;
}
```