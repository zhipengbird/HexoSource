---
title: NSTimer
date: 2016-08-12 17:01:59
tags:
---

## 一、创建

```objc
+ (NSTimer *)timerWithTimeInterval:(NSTimeInterval)ti invocation:(NSInvocation *)invocation repeats:(BOOL)yesOrNo;
+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)ti invocation:(NSInvocation *)invocation repeats:(BOOL)yesOrNo;

+ (NSTimer *)timerWithTimeInterval:(NSTimeInterval)ti target:(id)aTarget selector:(SEL)aSelector userInfo:(nullable id)userInfo repeats:(BOOL)yesOrNo;
+ (NSTimer *)scheduledTimerWithTimeInterval:(NSTimeInterval)ti target:(id)aTarget selector:(SEL)aSelector userInfo:(nullable id)userInfo repeats:(BOOL)yesOrNo;

- (instancetype)initWithFireDate:(NSDate *)date interval:(NSTimeInterval)ti target:(id)t selector:(SEL)s userInfo:(nullable id)ui repeats:(BOOL)rep NS_DESIGNATED_INITIALIZER;
```
<!-- more -->
注：
1. timerWithTimeInterval 需要手动添加到 runloop 中。
   scheduledTimerWithTimeInterval 不需要手动添加到 runloop 中，创建timer已直接添加到 runloop 中。

2. 一次性的timer在完成调用以后会自动将自己invalidate，而重复的timer则将永生，直到你显示的invalidate它为止。

3. repeats：timer是不一定准时的，是有可能被delay的，每次间隔的时间是不一定一样的。

>API: A repeating timer reschedules itself automatically based on the scheduled firing time, not the actual firing time. For example, if a timer is scheduled to fire at a particular time and every 5 seconds after that, the scheduled firing time will always fall on the original 5 second time intervals, even if the actual firing time gets delayed. If the firing time is delayed so much that it misses one or more of the scheduled firing times, the timer is fired only once for the missed time period. After firing for the missed period, the timer is rescheduled for the next scheduled firing time.

译文：一个repeat的timer，它在创建的时候就把每次的执行时间算好了，而不是真正启动的时候才计算下次的执行时间。举个例子，假如一个timer在一个特定的时间t激活，然后以间隔5秒的时间重复执行。那么它的执行操作的时间就应该为t, t+5, t+10,… 假如它被延迟了，例如实际上timer在t＋2的时候才启动，那么它下次执行的时间还是t＋5，而并不是t＋2+5，如果很不幸地在t＋5还没有启动，那么它理应该在t执行的操作就跟下一次t＋5的操作合为一个了。至于为什么会延迟呢，这就跟当前线程的操作有关，因为timer是同步交付的，所以假如当前线程在执行很复杂的运算时，那必须等待运算的完成才能调用timer，这就导致了timer的延迟。

## 二、使用（触发）

```objc
- (void)fire; //立即触发计时器
@property (copy) NSDate *fireDate; //触发时间
@property NSTimeInterval tolerance NS_AVAILABLE(10_9, 7_0); //误差值

```

计时器允许其触发比预定触发的日期晚,计时器可能在预定时间 到 预定时间 ＋ 误差值 之间被触发。预定触发之前不会触发。对于重复计时器,下一次触发时间是从原先的预定时间计算从而会单个触发的时间的误差值。默认值为0。系统有权运用少量的宽容某些计时器而忽略这个属性的值。

注：
1. timer 和 runloop
一个线程默认又一个runloop，子线程的runloop需要手动启动，在子线程中使用timer要启动NSRunLoop。当在非main thread中perform selector时，其thread中必须有一个激活的run loop。对于你自己创建的thread而 言，只有你的代码显式的运行一个run loop后该perform selector才能得到执行。Run loop在当loop运行时处理所有已排队的perform selector，而不是在一个loop循环时只处理某一个perform selector。

2. 确保runloop mode的匹配
通常是主线程的default mode，NSTimer与NSURLConnection默认运行在default mode下，这样当用户在拖动UITableView处于UITrackingRunLoopMode模式时，NSTimer不能fire,NSURLConnection的数据也无法处理。

### 附：runloop mode
  1. kCFRunLoopDefaultMode: 默认 mode，通常主线程在这个 Mode 下运行。

  2. UITrackingRunLoopMode: 追踪mode，保证Scrollview滑动顺畅不受其他 mode 影响。

  3. UIInitializationRunLoopMode: 启动程序后的过渡mode，启动完成后就不再使用。

  4. GSEventReceiveRunLoopMode: Graphic相关事件的mode，通常用不到。

  5. kCFRunLoopCommonModes: 占位mode，作为标记DefaultMode和CommonMode用。

3. timer 的后台任务

NSTimer 普通只可以在前台执行，进入后台则不能执行（坑：模拟器后台可运行，真机不行），但可以在app中设置定时执行的任务，使用：setKeepAliveTimeout: handler: 设置后台运行时的定时任务。

1、在Info.plist中添加UIBackgroundModes属性，即Required background modes，添加如VOIP、audio、location、蓝牙、download等。

2、使用setKeepAliveTimeout: handler:方法来注册一个周期性执行的任务, 而不管是否运行在后台.使用clearKeepAliveTimeout可以相应地清除该定时任务.

例：
```objc
[[UIApplication sharedApplication] setKeepAliveTimeout:600 handler:^{[self reportDeviceInfo];}];

```

## 三、释放

```objc
- (void)invalidate;

```

注：
1. timer 的释放与创建必须在同一线程。

2. timer 的强引用：
timer 添加到runloop中，会被 runloop 强引用。解决方法：invalidate 方法，并且 timer ＝ nil；使得timer从runloop中release掉，
target 是strong类型，timer 强引用target，如果在target是viewController的话，则在其dealloc方法里面释放timer是不行的！viewController本身就不会释放。
解决方法：建立 target 与 timer 的弱引用关系。

参考博客：http://blog.csdn.net/enuola/article/details/9163051
