---
title: GCD锁
date: 2016-08-16 16:41:23
categories: 
- iOS  
tags:
- GCD
---
> 何在GCD中快速的控制并发呢？
> 答案就是dispatch_semaphore


<!-- more -->
## 什么是dispatch_semaphore
dispatch_semaphore是持有计数的信号,该计数是多线程编程中的计数类型信号,类似于过马路的信号灯,红灯表示不能通过,而绿灯表示可以通过.而在dispatch_semaphore中使用计数来实现该功能,进行更细粒度的排他控制.在没有Serial Dispatch Queue和dispatch_barrier_async函数那么大的粒度且一部分处理需要进行排他控制的情况下,dispatch Semaphore便可发挥威力  
## dispatch_semaphore语法说明
 1. 通过`dispatch_semaphore_create(long value);`函数创建`Dispatch_Semaphore`,参数表示计数的初始值
```objc
//参数说明
//long value:表示计数的初始值
dispatch_semaphore_t semaphore = dispatch_semaphore_create(long value);
```

2. `dispatch_semaphore_wait(dispatch_semaphore_t dsema, dispatch_time_t timeout);`函数等待`Dispatch Semaphore`的计数值大于或者等于1,当满足条件时计数器执行减法,并从wait函数中返回
___当dispatch_semaphore_wait函数返回0时,可以安全地执行排他控制的处理___

```objc
//参数说明

//dispatch_semaphore_t dsema:操作的Dispatch_Semaphore对象

//dispatch_time_t timeout:由dispatch_time_t类型值指定等待时间

dispatch_semaphore_wait(dispatch_semaphore_t dsema, dispatch_time_t timeout);
```
3. `dispatch_semaphore_signal(dispatch_semaphore_t dsema);`函数将 `Dispatch_Semaphore `的计数器加1

```objc
//参数说明
//dispatch_semaphore_t dsema:操作的Dispatch_Semaphore对象
dispatch_semaphore_signal(dispatch_semaphore_t dsema);
```
## 示例代码
  没有加锁的情况下，异步添加1000个数据到数组中
  ```objc
  void unusesemaphoreSameple(){
     //1.创建全局队列
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
     //2.创建保存数据的可变数组
    NSMutableArray * array = [NSMutableArray array];
    //执行10000次操作
    for (int i =0 ; i<1000; i++) {
         //异步添加数据
        dispatch_async(queue, ^{
             //处理数据
            [array addObject:@(i)];
        });
    }
    NSLog(@"%@",array);
}
  ```
  执行结果是：代码出现问题，不能正常添加数据
  ```
  GCDdispatch_semaphore(23505,0x700000081000) malloc: *** error for object 0x100700330: double free
*** set a breakpoint in malloc_error_break to debug
  ```
  加锁情况下，异步添加1000个数据到数组中
  ```objc
  void usesemaphoreSameple(){
    //1.创建全局队列
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    //2.创建dispatch_semaphore_t对象
    dispatch_semaphore_t semaphore = dispatch_semaphore_create(1);
    //3.创建保存数据的可变数组
    NSMutableArray * array = [NSMutableArray array];
    //执行10000次操作
    for (int i =0 ; i<1000; i++) {
        //异步添加数据
        //数据进入,等待处理,信号量减1
        dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
        dispatch_async(queue, ^{
            //处理数据
            [array addObject:@(i)];
            //数据处理完毕,信号量加1,等待下一次处理
            dispatch_semaphore_signal(semaphore);
        });
    }
    NSLog(@"%@",array);
}
  ```
   执行结果：按顺序正常添加了1000个数据
   ```objc
   0,
   1,
   2,
   3,
   4,
   5,
   6,
   7,
   8,
   9,
   10,
   11,
   12,
   13,
   14,
   15,
   16,
   17,
   18,
   19,
   20,
   21,
   22,
   ....
   ```
