---
title: 'UIView中的坐标转换convertPoint '
date: 2017-03-11 09:33:14
categories: iOS
tags:
  -坐标转换
---

UIView为我们提供了查找视图所在行的简洁方法,实现视图坐标系间的坐标转换：
-convertPoint:toView: 方法

常用转换方法
```objc
// 将像素point从view中转换到当前视图中，返回在当前视图中的像素值
- (CGPoint)convertPoint:(CGPoint)point fromView:(UIView *)view;



// 将rect由rect所在视图转换到目标视图view中，返回在目标视图view中的rect
- (CGRect)convertRect:(CGRect)rect toView:(UIView *)view;


// 将rect从view中转换到当前视图中，返回在当前视图中的rect
- (CGRect)convertRect:(CGRect)rect fromView:(UIView *)view;

```
