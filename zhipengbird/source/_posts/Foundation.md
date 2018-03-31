---
title: ARC 和 Core Foundation
date: 2018-02-04 17:50:59
tags:
---
# Core Foundation

Core Foundation 杠框架使用一个朴素的C语言编写的纯手工创建的对象系统。CF对象具有它们自己的音独的引用计数系统。从其名称中具有的单词"Create"、"Copy"的函数获得的对象在返回时将具有“+1的保留计数”。CFRetain(aCFObject)函数将把aCFObject的保留计数加1，而CFRelease(aCFObject)函数则会把aCFObject的保留计数减1。 当一个对象的保留计数归零时，它将被取消分配。如果你从一个“Create”、“Copy”函数接收到对象，最终必须通过调用CFRelease来抵消创建或复制。如果不这样做，就会产生内存泄漏。

ARC不会使用这个单独的引用计数系统自动化。 **如果你在使用CF对象，就要负责理解手动引用计数，并且正确地保留和释放你的CF对象**

# ARC 与 Core Foundation 之间的免费桥接

从免费桥接的一方到别一方的强制转换对于ARC而言都是一个问题。当把一个对象从CF强制转成OC(或者采用相反方向)时，结果将得到单个对象，它的保留计数可以被两个不同的不会彼此交流的系统操纵。ARC对CF的内存管理约定一无所知，使事情更坏的是，你可以直接控制其中一个系统（CF），但不能控制另一个系统ARC。***就像由两位厨师一起做一碗汤一样，会引起烹饪麻烦。***
编译器避免这种麻烦的方式是禁止在OC对与CF对象之间进行强制转换。解决这个问题的方式是：**通过一种称为 ***桥接强制转换（brige casts）*** 的特殊的强制转换类型给ARC提供一些额外的信息** 有以下三种情况

* 强制转换不转移所有权
* 强制转换把所有权转出CF并转入ARC中
* 强制转换把所有权转出ARC 并转入CF中

## 强制转换不会转移所有权
在这种情况下，一方只会从另一方临时“借用”对象，而不会转移所有权
`__bridge`告诉ARC在强制转换下不会转移所有权。
{% codeblock CF-->OC  lang:objc %}
    // CF---->OC
    CFStringRef cfstr =  CFStringCreateWithCString(NULL, "hello cfstring!", kCFStringEncodingUTF8);
    NSString * ocstr = (__bridge NSString*)cfstr;
    //只是类型的转换，没有进行所有权的交接
    NSLog(@"%@",ocstr);
    CFRelease(cfstr);
    // 内存管理仍需要所手动释放

{% endcodeblock %}

{% codeblock OC --> CF   lang:objc   %}
    //OC --> CF
    NSString * ocString = @"hello objective C string!";
    CFStringRef cfstring = (__bridge CFStringRef)ocString;
    // 只是类型转换，对象释放仍由ARC来进行管理，不需要进行手动释放该cfstring
{% endcodeblock %}

{% codeblock OC-->CF 在参数中的使用  lang:objc   %}
 // 生成gif文件路径
    NSString * gifpath = [imgDir stringByAppendingPathComponent:@"newgif.gif"];
    CFURLRef  urlref = CFURLCreateWithFileSystemPath(kCFAllocatorDefault, (__bridge CFStringRef)gifpath, kCFURLPOSIXPathStyle, NO);
    CFRelease(urlref);
{% endcodeblock %}

{% codeblock OC-->CF 在参数中使CFBridgingRetain会造成内存泄漏  lang:language url link text %}
    NSString * gifpath = [imgDir stringByAppendingPathComponent:@"newgif.gif"];
    CFURLRef  urlref = CFURLCreateWithFileSystemPath(kCFAllocatorDefault, CFBridgingRetain(gifpath)/**只做类型的转换，不需要移交所有权*/, kCFURLPOSIXPathStyle, NO);//这里会造内存泄漏
{% endcodeblock %}

## 强制转换把所有权转出CF并转入ARC中
`__bridge_transfer`强制转换将把所有权从CF转移给ARC。它告诉ARC在CF一方有一个没有抵消的保留计数，最终必须通过一个release抵消它。ARC负责发送该release消息
{% codeblock 对象所有权转出CF，并转入ARC中  lang:objc   %}
    CFStringRef cfstring = CFStringCreateWithCString(NULL, "hello CFStringRef object", kCFStringEncodingUTF8);
    NSString * ocString = (__bridge_transfer NSString*)cfstring;
    // ARC 将接替CF的所有权，负责ocString的释放，其将会在不再使用ocString对象时，向该对象发送一条release消息
    NSLog(@"%@",ocString);
{% endcodeblock %}

## 强制转换把所有权转出ARC并转入CF中
`__bridge_retain`强制转换采用的是相反的方向，它把所有权从ARC中转移给CF。它告诉ARC抵消对象创建的责任传递给了CF。当CF代码用完对象后，它必须利用对象作为参数来调用`CFRelease()`
{% codeblock  %}
    NSString * ocObject = @"我将放器ARC的内存管理，转入CF的内存管理系统";
    CFStringRef cfObject = (__bridge_retained CFStringRef)ocObject;
    // CF接管现OC对象的所有权，它的释放由CF来负责,必须手动进行释放
    CFRelease(cfObject);
{% endcodeblock %}

## 利用宏隐藏强制转换
可以利用宏`CFBrigingRelease()`和`CFBrigingRetain()`分别隐藏`__bridge_transfer`和`__bridge_retain`强制转换，来改进代码的美感。
{% codeblock 以下代码是等价的     %}
    // 以下两行代码将做相同的事情
    NSString * ocString = (__bridge_transfer NSString*)cfstring;
    NSString * ocString = CFBridgingRelease(cfstring);

    // 以下两行代码将做相同的事情
    CFStringRef cfObject = (__bridge_retained CFStringRef)ocObject;
    CFStringRef cfObject = CFBridgingRetain(ocObject);
{% endcodeblock %}


## 与void*之间来回进行强制转换
ARC禁止在对象指针与类型化`void*`的变量之间直接进行强制转换。
其原因与ARC禁目对象指针与CF类型之间直接进行大多数强制转换原因相同。如果把一个对象指针强制转换成`void*`，将把该对象提供给一个未知量。ARC不会管理非OC指针，因为它无法跟踪一个`void*`变量中存储的对象所发生的事情，别外，如果把一个`void*`指针强转为OC对象，ARC将无法确定对象的所有权状态。
如果确信知道自己正在做什么，可以使用桥接来转换支配ARC：
{% codeblock     %}
    NSMutableArray * array = [NSMutableArray arrayWithObject:@"hello OC object"];
    //    void * cPointer = (void*)array;//Cast of Objective-C pointer type 'NSMutableArray *' to C pointer type 'void *' requires a bridged cast  这样编译器会不报错
    void * cPointer = (__bridge void*)array;//这样是OK 的
{% endcodeblock %}
***小心：  __bridge 强制转没有内存管理的隐含意思，并且`void*`不是强引用，如果指向对象的所有的强引用都消失了，就会取消分配对象，并且`void*`变理可能保存一个指向死对象的指针。 ***

# ARC 和额外的自动释放池
使用ARC，你将不知道特定的对象是否将被释放或者自动释放，或者它将被取消分配的确切时间，不过当你的代码将创建许多只在短时间内需要的对象时，仍然可以使用额外的自动释放时来限制内存占用：
```
   for (int i = 0; i<10000; i++) {
        @autoreleasepool{
            // 创建监时的对象
        }
    }
```
@autoreleasepool的作用域确保：在循环的单独一次迭代中创建的任何临时对象都会在不晚于循环的底部位置收到一个release消息，在那个位置@autoreleasepool的作用域将终结，并会清空对应的自动释放池，ARC可能决定在更早的时间给这样的对象发送一条release消息，而不是自动释放它们，但是额外的@autoreleasepool作用域将会阴止随着循环的每次迭代而使用当前自动释放池中的对象数量增加的情况发生。

