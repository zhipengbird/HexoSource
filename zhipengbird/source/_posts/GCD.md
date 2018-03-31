---
title: GCD
date: 2018-01-27 13:55:43
tags:
---
# GCD
GCD(Grand Central Dispatch)是一个能让开发者轻松写出并发代码的强大特性。它把管理多个线程及线程同步的重担转移给了操作系统。使用GCD时，只需要创建能够彼此独立执行的单元，而让操作系统为你处理队列和同步。iOS上的GCD实现了一系列包含了一系列的C语言扩展、API、和一个运行时引擎。如果有多个处理器，GCD会自动确保独立单元运行在多个处理器上。
GCD提供了三种派发队列：**串形队列**、**并发队列**和**主队列**。可以把任务加入这些队列。串形队列按先进先出（FIFO）的顺序，在同一时间只执行一个任务。并发队列也是按照FIFO顺序，但并行地执行任务。主队列在主线程上执行操作，通常用于不同的串行或并发队列上执行线程间的同步。
GCD是iOS多任务的核心，广泛应用在系统在系统层的几乎各个方面。NSOPerationQueue是GCD的基础上实现的，而基本的队列原理都类似。只要把一个块添加到分派队列，而不是把NSOperation添加到NSOperationQueue。
分派队列要比操作队列更底层。块添加到分配队列后就无法取消了。分派队列是严格FIFO结构。所以无法在队列中使用优先级或者调整块的次序。
## 源和定时器
分派源是一种方便的事件处理方法。先注册处理事件，当事件发生后，你就能得到通知。如果在系统还没有来得及通知你之前事件就已经发了多次，那么这些事件会被合并为一个事件。这对于底层的高性能I/O代码很有用。分派源可以应答UNIX信号、文件系统变化、其他进程的变化以及mach port事件。它们在MAC上很有用。
***定时器源***在iOS上很有用。GCD定时器源基于分派队列。而不是像NSTimer那条基于运行循环，这意味着把它们用于多线程应用中要容易得多。GCD定时器不用块而不是用选择器。所以不需要一个独立的方法处理定时器，这样也更容易避免重复GCD定时器的循环保留。
***分派屏障（dispathc barrier）***可以在并发队列内部创建一个同步点，当它运行时，即使用有并发条件和空闲的处理器核心，队列中也不能运行。听起来像互斥（写入）锁，确实如此。没有屏障的块可以看作共享（读取）锁。只要所有对资源的访问都要通过这个队列的进行，那么屏障就能以极低的代价提供同步。
作为比较，可以使用@syschronize管理多线和访问，它会在参数上加一个互斥锁。

## 队列目标和优先级
GCD中的队列是有层次结构的，只有全局队列才是调度运行的，可以用`dispatch_get_global_queue`和下列某个优先级参数来访问这些队列
* DISPATCH_QUEUE_PRIORITY_HIGH
* DISPATCH_QUEUE_PRIORITY_DEFAULT
* DISPATCH_QUEUE_PRIORITY_LOW
* DISPATCH_QUEUE_PRIORITY_BACKGROUND
所有这些队列都是并发的，GCD调度与HIGH队列中线程数量相同的块。当high队列空了以后就转移到default队列。以些类推。系统根据当前的空余处理器核心数量和系统负载按需创建或销毁线程。
创建队列时，它被连接到某个全局对队列（全局队列就是它的目标）。默认情况下连接到default队列。当一个块到了队列的最前面，块其实就到了目标队列的最尾。块到了全局队列的最前面才会执行，可以使用`dispatch_set_target_queue`改变目标队列

## 分派组
分派组类似于NSOpertion中的依赖关系。首先创建一个组
{% codeblock 创建组  lang:objc   %}
    dispatch_group_t group = dispatch_group_create();
{% endcodeblock %}

*PS. 组本身没有任何配置项，它们没有绑到任何队列上，只是一个组块。一般通过`dispatch_group_async`把块添加到组，类似`dispatch_async`:
{% codeblock 加入块  lang:objc   %}
  dispatch_group_async(group, queue, ^{
        
    });
{% endcodeblock %}
然后调用`dispatch_gourp_notify`注册一个块，即当组执行完毕后调用它
{% codeblock 执行完后执行该块  lang:objc   %}
dispatch_group_notify(group, queue, ^{
        
    });
{% endcodeblock %}
组里所有的块执行完毕时，block就会被调用到queue上，可以注册同一个组的多个通知。可以把这些通知块放到不同的队列上。如果调用`dispatch_group_notify`时队列上没有任何块，那么马上触发通知。可以在配置组上时用`dispatch_supend`暂停队列来防止这种情况，配置完后用`dispatch_resume`启动队列。
实际上组并不像追踪任务一样追踪块，可以直接使用`dispatch_group_enter`和`dispatch_group_leave`增加和减少任务数量。
{% codeblock  %}
    dispatch_async(queue, ^{
        dispatch_group_enter(group);
        dispatch_sync(queue, ^{
            
        });
        dispatch_group_leave(group);
    });
{% endcodeblock %}
调用`dispatch_group_wait`会阻塞当前线程，直到整个组执行完毕。 
# NSOperationQueue
## 什么时候应该使用GCD，什么时候应该使用NSOperationQueue?
两者的相同点与异点：
1. NSOperationQueue 用GCD创建，是GCD的高级抽象
2. GCD只支持FIFO队列,而加入NSOperationQueue队列的操作可以被重新排序（重新设置优先级）
3. NSOperationQueue支持在操作之间设置依赖关系，而GCD不支持。如果一个操作需要别一个操作生成的数据，可以让这个操作依赖别一个操作。NSOpertionQueue会自动按照正确的顺序执行。而GCD没有内建依赖关系支持
4. NSOperationQueue 兼容KVO. 这意味着你可以观察任务的状态。那么我们应该只用NSoperationQueue而不用GCD吗？答案是否定的。NSOperationQueue的执行速度比GCD慢.如果用Instruments查看代码性能并且觉得需要性能提升的话，那就用GCD。通常情况下底层代码中，任务之间可能不会相互依赖或者没有必要用KVO观察状态。

# 多任务
多任务最基本的形式就是运行循环，多任务最早在NeXTSTEP上出现时使用的就是这种形式
每个iOS程序都是由一个处于阻塞状态的do/while循环驱动，当有事件发生时，就把事件分派给合适的监听器。如此反复直至循环结束。处理分派的对象就叫作运行循环（NSRunloop）
> 运行循环其实就是一个大的do/while循环，它运行在某个线程中，从各种事件队列中取得事件（每次取一个），然后把它分派给合适的监听器。这就是iOS的核心

NSTimer基于运行循环进行消息派发。调度NSTimer时，其实是要求当前的运行循环在某个特定的时间分派某个选择器。运行循环的每次迭代都会对时间进行检查并且触发所有过期的选择器，延迟执行的方法也是通过调度定时器实现的。
## NSOperation
NSOperation是封装任务的基本单位，在OC中用块来表示，NSOperation支持优先级、依赖关系和取消
NSOperationQueue尝试管理生成的线程数，但是大多数情况下，它做得并不好。NSOperationQueue会同时调度太多的操作，如果明确告诉NSOperationQueue不要超过CPU数量的计算型密集操作，那么简单代码就跑得更快了。
{% codeblock 获取CPU核数 %}
#include <sys/sysctl.h>
unsigned int countOfCores() {
    unsigned int ncpu;
    size_t len = sizeof(ncpu);
    sysctlbyname("hw.ncpu", &ncpu, &len, NULL, 0);
    return ncpu;
}
{% endcodeblock %}
