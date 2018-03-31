---
title: iOS手机锁屏控制
date: 2016-08-19 18:42:01
categories: 
- iOS
tags:
- iOS
---
```objc
 [[UIApplication sharedApplication] setIdleTimerDisabled:YES];//禁止自动锁屏
  [[UIApplication sharedApplication] setIdleTimerDisabled:NO];//允许自动锁屏
```
